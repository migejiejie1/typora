```shell
mtr
相关命令：暂无相关命令
1.Mtr介绍：
Mtr是 Linux中有一个非常棒的网络连通性判断工具，它结合了ping, traceroute,nslookup 的相关特性。
apt-get install mtr -y
安装mtr工具
2.Mtr的相关参数：
mtr -s
用来指定ping数据包的大小
mtr -nno-dns
不对IP地址做域名解析
mtr -a
来设置发送数据包的IP地址 这个对一个主机由多个IP地址是有用的
mtr -i
使用这个参数来设置ICMP返回之间的要求默认是1秒
mtr -4
IPv4
mtr -6
IPv6

=========================================================

[root@vpn208 conf]# mtr -r www.baidu.com
HOST: vpn208                      Loss%   Snt   Last   Avg  Best  Wrst StDev
  1. 192.168.10.1                  0.0%    10    0.4   0.4   0.3   0.4   0.0
  2. 101.81.96.1                   0.0%    10  307.6 562.0 307.6 790.5 154.4
  3. 124.74.124.157                0.0%    10    6.4   5.4   3.0   6.7   1.1
  4. 101.95.42.201                 0.0%    10    7.1   5.7   3.7   7.4   1.2
  5. 202.101.63.130                0.0%    10   11.8   5.9   3.4  11.8   2.4
  6. 202.97.68.142                 0.0%    10    7.4   8.4   7.0   9.7   1.2
  7. 220.191.200.98                0.0%    10    5.9   7.4   5.9  14.0   2.5
  8. ???                          100.0    10    0.0   0.0   0.0   0.0   0.0
  9. 115.239.209.6                 0.0%    10    6.4   7.0   6.4   7.9   0.5
 10. ???                          100.0    10    0.0   0.0   0.0   0.0   0.0
 11. 115.239.210.27                0.0%    10    6.2   7.5   5.7  14.8   2.7
 
第一列：显示的是IP地址和本机域名，这点和tracert很像。

第二列 Loss%：是显示的每个对应IP的丢包率。

第三列 snt：snt等于10，设置每秒发送数据包的数量，默认值是10 可以通过参数 -c来指定。

第四列 Last：显示的最近一次的返回时延。

第五列 Avg：平均值，这个应该是发送ping包的平均时延。

第六列 Best：最好或者说时延最短的时间。

第七列 Wrst：最坏或者说时延最长的时间。

第八列 StDev：标准偏差。


# mtr -r -c 30 www.baidu.com   #设置每秒发送数据包的数量30

# mtr -r -c 30 -s 1024 www.baidu.com    #设置ping包大小为1024个字节。


```

