```
1. docker指定网桥
docker默认使用docker0网桥，网段为172.17.0.1/24，如果需要我们可以指定使用使用自己定义的网桥。

指定网桥：

首先需要创建使用的网桥

brctl addbr bridge0
 
ip  addr add 192.168.111.1/24 dev bridge0 
 
ip link set dev bridge0 up 
然后在 /etc/dcoker/daemon.json(没有改文件，可创建）中添加如下信息：

"bridge":"bridge0"
重新启动docker

systemctl restart docker
此时docker 使用的网桥和网段将是你自定义的

2. 指定网段一
有时候我们只需要修改网段，可以在/etc/docker/daemon.json中添加如下信息:

"bip":"192.168.110.1/24"
重启docker,此时使用指定的网段

3. 指定网段二
上述两种方法适合安装docker-ce以及docker-x86_64，当安装的docekr.x86-64版本，还有另外一种方法指定网段。

在/etc/sysconfig/docker-netwoek中指定网段

DOCKER_NETWORK_OPTIONS= --bip=192.18.0.1/16
```

```
我们在局域网中使用Docker，最常遇到的一个困惑，就是有时候跨网段结果出现网络不通。原因是因为Docker默认生成的网关和我们的局域网网段有时候是冲突的，比如在172.16网段的机器上部署Docker，结果生成的docker0网桥是172.17网段，那么就和真实环境中使用该网段的机器冲突了（即ping不通172.17网段的机器）。为了避免冲突，首先想到的是改网关，举例如下（以Centos为例）：

service docker stop
# 删除docker防火墙过滤规则
iptables -t nat -F POSTROUTING
# 删除docker默认网关配置
ip link set dev docker0 down
ip addr del 172.17.0.1/16 dev docker0
# 增加新的docker网关配置
ip addr add 192.168.2.1/24 dev docker0
ip link set dev docker0 up
# 检测是否配置成功，如果输出信息中有 192.168.5.1，则表明成功
ip addr show docker0
service docker start
# 验证docker防火墙过滤规则
       这么改完，是否就可靠了？答案是否定的，因为docker重启后，可能还是会重建docker0，覆盖我们所做的修改。说明Docker的IP规则就是写死的，不让我们随便更改。但我们就换个思路，直接干掉docker0，重建一个新的网桥：

首先需要安装网桥创建工具brctl：

sudo yum install -y bridge-utils
开始创建操作：

# 1.停止 Docker 服务
service docker stop

# 2.创建新的网桥（新的网段）
brctl addbr bridge0
ip addr add 192.168.2.1/24 dev bridge0
ip link set dev bridge0 up

# 3.确认网桥信息
ip addr show bridge0
# 4.修改配置文件
/etc/docker/daemon.json（如不存在则创建一个 touch daemon.json）,使Docker启动时使用自定义网桥

{
  "bridge": "bridge0"
}
# 5.重启 Docker
service docker start
    
# 确认 NAT 网络路由
iptables -t nat -L -n

# 6.删除不再使用的网桥
ip link set dev docker0 down

brctl delbr docker0

iptables -t nat -F POSTROUTING
关于第4步所做的修改配置，就是引用新的网桥，其实还可以在docker配置文件中引用新的网桥：

echo 'DOCKER_OPTS="-b=bridge0"' >> /etc/sysconfig/docker
sudo service docker start
但是不代表我们一定能看到docker自定义配置文件，如果没有default/docker或sysconfig/docker，比较麻烦，解决方法如下：

$ vi /lib/systemd/system/docker.service
#添加一行
$ EnvironmentFile=-/etc/default/docker
或者
$ EnvironmentFile=-/etc/sysconfig/docker
#-代表ignore error

#并修改
$ ExecStart=/usr/bin/docker daemon -H fd://
#改成
$ ExecStart=/usr/bin/docker daemon -H fd:// $DOCKER_OPTS
#这样才能使用/etc/default/docker里定义的DOCKER_OPTS参数

$ systemctl daemon-reload 重载
$ sudo service docker restart
完成了bridge0的创建和从docker0过度到bridge0，那么我们就可以route一下，以确认是否有我们不想看到的172.17网段：



只要没有，那么我们就不但心和172.17网段的机器连通了。如果还有，那么再用 ip addr del 172.17.0.1/16 dev docker0，直到清除完毕（因为已经建立新的docker网桥，所以删除旧的不会影响docker使用）。

如果重启机器后brctl所创建的网桥可能丢失，那么我们可以将以下命令写到linux自启动脚本中，每次重启的时候执行一遍：

brctl addbr bridge0
ip addr add 192.168.2.1/24 dev bridge0
ip link set dev bridge0 up
自启动脚本可以通过在/etc/rc.local文件中添加可执行语句（如 sh /opt/script.sh &）。这样基本上每次重启机器后，也能保证bridge0被创建，确保docker服务正常启动。
```

修改docker默认网段的两种方法

1. 创建网桥 然后使用新建的网桥作为默认网桥（非自定义网桥）

```
 [root@VM_0_12_centos ~]# yum install bridge-utils
Loaded plugins: fastestmirror, langpacks
Repository epel is listed more than once in the configuration
docker-ce-stable                                                                                                                                                                                       | 3.5 kB  00:00:00
epel                                                                                                                                                                                                   | 3.2 kB  00:00:00
extras                                                                                                                                                                                                 | 3.4 kB  00:00:00
mysql-connectors-community                                                                                                                                                                             | 2.5 kB  00:00:00
mysql-tools-community                                                                                                                                                                                  | 2.5 kB  00:00:00
mysql57-community                                                                                                                                                                                      | 2.5 kB  00:00:00
nodesource                                                                                                                                                                                             | 2.5 kB  00:00:00
os                                                                                                                                                                                                     | 3.6 kB  00:00:00
updates                                                                                                                                                                                                | 3.4 kB  00:00:00
Loading mirror speeds from cached hostfile
Package bridge-utils-1.5-9.el7.x86_64 already installed and latest version
Nothing to do
[root@VM_0_12_centos ~]# brctl addbr br0
device br0 already exists; can't create bridge with the same name 
[root@VM_0_12_centos ~]# brctl addbr br1 
[root@VM_0_12_centos ~]# ip addr add 10.122.0.0/16 dev br1
[root@VM_0_12_centos ~]# ip link set dev br1 up
[root@VM_0_12_centos ~]# ip addr show br1
123: br1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN qlen 1000
   link/ether ee:6e:b7:fb:4a:97 brd ff:ff:ff:ff:ff:ff
   inet 10.122.0.0/16 scope global br1
      valid_lft forever preferred_lft forever
[root@VM_0_12_centos ~]# vim /etc/docker/daemon.json
[root@VM_0_12_centos ~]# cat /etc/docker/daemon.json
{
 "bridge": "br1"
}
[root@VM_0_12_centos ~]# systemctl restart docker.service
[root@VM_0_12_centos ~]# docker run  -it --rm busybox cat /etc/hosts
127.0.0.1	localhost
::1	localhost ip6-localhost ip6-loopback
fe00::0	ip6-localnet
ff00::0	ip6-mcastprefix
ff02::1	ip6-allnodes
ff02::2	ip6-allrouters
10.122.0.3	51c410e24a69  

```

```
[root@VM_0_12_centos ~]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
03faaf61101a        bridge              bridge              local
d08980b50bd0        bridge0             bridge              local
7a093c39f225        bridge3             bridge              local
117f13eb9ab8        host                host                local
206f66306972        none                null                local
[root@VM_0_12_centos ~]# docker network inspect bridge -f '{{json  .Options }}'
{"com.docker.network.bridge.default_bridge":"true","com.docker.network.bridge.enable_icc":"true","com.docker.network.bridge.enable_ip_masquerade":"true","com.docker.network.bridge.host_binding_ipv4":"0.0.0.0","com.docker.network.bridge.name":"br1","com.docker.network.driver.mtu":"1500"}
[root@VM_0_12_centos ~]# docker network inspect bridge -f '{{index  .Options "com.docker.network.bridge.name"}}'
br1

```

容器的ip 显示docker的[网桥](https://so.csdn.net/so/search?q=网桥&spm=1001.2101.3001.7020)设置已生效

2. 修改默认网桥[网段](https://so.csdn.net/so/search?q=网段&spm=1001.2101.3001.7020)（docker0）

```
[root@VM_0_12_centos ~]# vim /etc/docker/daemon.json
[root@VM_0_12_centos ~]# cat /etc/docker/daemon.json
{
"bip": "10.125.0.1/16"
}
[root@VM_0_12_centos ~]# systemctl restart docker
[root@VM_0_12_centos ~]# docker run  -it --rm  busybox cat /etc/hosts
127.0.0.1	localhost
::1	localhost ip6-localhost ip6-loopback
fe00::0	ip6-localnet
ff00::0	ip6-mcastprefix
ff02::1	ip6-allnodes
ff02::2	ip6-allrouters
10.125.0.3	e294ad32d849
[root@VM_0_12_centos ~]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
da4f0fc850ad        bridge              bridge              local
d08980b50bd0        bridge0             bridge              local
7a093c39f225        bridge3             bridge              local
117f13eb9ab8        host                host                local
206f66306972        none                null                local
[root@VM_0_12_centos ~]# docker network inspect bridge -f '{{index  .Options "com.docker.network.bridge.name"}}'
docker0

```

上述是两种方法修改docker容器网段的方法， 本质是一样的（原因下述），都是docker的默认网桥，只是名字不一样，并不是自定义网桥，因此推荐第二种修改docker网段的方法。 而docker自定义网桥的功能，只有在docker network 创建的才有自定义网桥的种种便利及特性。

## docker 自定义网桥

自定义网桥的优势

- 可以指定别名， 基于docker内部的DNS 实现别名解析ip
- 可以指定容器一个固定ip

1. 首先测试下默认网桥是否可以是使用上述功能（切换至br1网桥）

```
[root@VM_0_12_centos ~]# vim /etc/docker/daemon.json
[root@VM_0_12_centos ~]# cat /etc/docker/daemon.json
{
"bridge": "br1"
}
[root@VM_0_12_centos ~]# systemctl restart docker
[root@VM_0_12_centos ~]# docker run  -it --rm  busybox tail -n 1 /etc/hosts
10.122.0.3	4be7569b8933
[root@VM_0_12_centos ~]# ip addr show br1
123: br1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP qlen 1000
    link/ether fa:dd:bd:81:e2:14 brd ff:ff:ff:ff:ff:ff
    inet 10.122.0.1/16 brd 10.122.255.255 scope global br1
       valid_lft forever preferred_lft forever
[root@VM_0_12_centos ~]# docker run   --network-alias my1 --hostname my1 -it busybox sh
docker: Error response from daemon: network-scoped alias is supported only for containers in user defined networks.
[root@VM_0_12_centos ~]# docker run  --rm --ip 10.122.4.3   -it busybox sh
docker: Error response from daemon: user specified IP address is supported on user defined networks only.
[root@VM_0_12_centos ~]# # 上述报错说明： 重新创建的br1 并不是自定义网桥
[root@VM_0_12_centos ~]# # 创建自定义网桥
[root@VM_0_12_centos ~]# docker network create  --gateway=10.237.0.1 --subnet=10.237.0.0/16 br2
9f618061c9bd3d7ad8d41229e956504fe1a01dc6cb7b4ffd3557561d2d83736b
[root@VM_0_12_centos ~]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
9f618061c9bd        br2                 bridge              local
49afa1fe5079        bridge              bridge              local
d08980b50bd0        bridge0             bridge              local
7a093c39f225        bridge3             bridge              local
117f13eb9ab8        host                host                local
206f66306972        none                null                local

```

2. 使用自定义网桥 ，并创建网络别名，hostname 查看ip

```
/ # [root@VM_0_12_centos ~]#  docker run  --network br2 --network-alias my1 --hostname my01 -it busybox sh
/ # cat /etc/hosts
127.0.0.1	localhost
::1	localhost ip6-localhost ip6-loopback
fe00::0	ip6-localnet
ff00::0	ip6-mcastprefix
ff02::1	ip6-allnodes
ff02::2	ip6-allrouters
10.237.0.2	my01

```

3. 使用自定义网桥 ，并创建网络别名，hostname 并指定固定ip查看ip

```
/ # [root@VM_0_12_centos ~]#  docker run  --network br2 --network-alias my2 --hostname my02 --ip 10.237.255.10 -it busybox sh
/ # ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
151: eth0@if152: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue
    link/ether 02:42:0a:ed:ff:0a brd ff:ff:ff:ff:ff:ff
    inet 10.237.255.10/16 brd 10.237.255.255 scope global eth0
       valid_lft forever preferred_lft forever
/ # # ping my1
/ # ping my1
PING my1 (10.237.0.2): 56 data bytes
64 bytes from 10.237.0.2: seq=0 ttl=64 time=0.081 ms
64 bytes from 10.237.0.2: seq=1 ttl=64 time=0.093 ms
^C
--- my1 ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
round-trip min/avg/max = 0.081/0.087/0.093 ms
/ #

```

4. 设定多个容器 网络别名为一个，测试可达到负载均衡的目的

```
# 启动三个统一网络别名的容器
[root@VM_0_12_centos ~]#  docker run  --network br2 --network-alias my1 -d --hostname my02 --ip 10.237.255.11 -it busybox
03b2ee5820e3718a8353ab900c586e0a4f2e2856a2bbf335039663e1d58f98f7
[root@VM_0_12_centos ~]#  docker run  --network br2 --network-alias my1 -d --hostname my02 --ip 10.237.255.13 -it busybox
11a3eb276ef4c94f195838162cf7894017fb6694d7fc54528e45558220238e12
[root@VM_0_12_centos ~]#  docker run  --network br2 --network-alias my1 -d --hostname my02 --ip 10.237.255.12 -it busybox
faf567c53db044cc8b5f22b3aba9c8f09b062d27a04df3a5cdaf4e4556bc194d
[root@VM_0_12_centos ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                                        NAMES
faf567c53db0        busybox             "sh"                     3 minutes ago       Up 3 minutes                                                     gallant_golick
11a3eb276ef4        busybox             "sh"                     3 minutes ago       Up 3 minutes                                                     thirsty_nightingale
03b2ee5820e3        busybox             "sh"                     3 minutes ago       Up 3 minutes                                                     amazing_sinoussi
e9f3d2fe4a8d        busybox             "sh"                     40 minutes ago      Up 40 minutes                                                    distracted_babbage
2c582cb994ed        jpillora/dnsmasq    "webproc --config /e…"   46 hours ago        Up About an hour    0.0.0.0:53->53/udp, 0.0.0.0:5380->8080/tcp   dnsmasq
[root@VM_0_12_centos ~]# for i in {1..10};do docker run --rm --network br2   --hostname my02 --ip 10.237.255.15 -it busybox ping -c 1 my1 |head -2;done
PING my1 (10.237.255.12): 56 data bytes
64 bytes from 10.237.255.12: seq=0 ttl=64 time=0.080 ms
PING my1 (10.237.255.12): 56 data bytes
64 bytes from 10.237.255.12: seq=0 ttl=64 time=0.095 ms
PING my1 (10.237.255.11): 56 data bytes
64 bytes from 10.237.255.11: seq=0 ttl=64 time=0.087 ms
PING my1 (10.237.255.11): 56 data bytes
64 bytes from 10.237.255.11: seq=0 ttl=64 time=0.080 ms
PING my1 (10.237.255.12): 56 data bytes
64 bytes from 10.237.255.12: seq=0 ttl=64 time=0.088 ms
PING my1 (10.237.255.13): 56 data bytes
64 bytes from 10.237.255.13: seq=0 ttl=64 time=0.085 ms
PING my1 (10.237.255.13): 56 data bytes
64 bytes from 10.237.255.13: seq=0 ttl=64 time=0.095 ms
PING my1 (10.237.255.13): 56 data bytes
64 bytes from 10.237.255.13: seq=0 ttl=64 time=0.091 ms
PING my1 (10.237.255.12): 56 data bytes
64 bytes from 10.237.255.12: seq=0 ttl=64 time=0.121 ms
PING my1 (10.237.255.13): 56 data bytes
64 bytes from 10.237.255.13: seq=0 ttl=64 time=0.100 ms


```

5. 删除所有测试的镜像

```
[root@VM_0_12_centos ~]# docker rm -f `docker ps -a|grep busybox |cut -f 1 -d' '`

```

