**docker配置代理解决无法pull**

在公司内网环境搭建的测试虚拟机，通过桥接的方式将该虚拟机部署在局域网内。由于公司内网没有直接使用外网的权限，所以上网都需要通过配置代理来实现。这里我在服务器的/etc/profile文件中配置了网络代理可以正常的进行软件安装，但是在使用[docker](https://so.csdn.net/so/search?q=docker&spm=1001.2101.3001.7020)时却发现docker命令好像使用不了这个代理(拉取不了镜像)。

尝试在内网环境下访问了下Docker Hub可以载入，说明当前的代理时允许访问Docker库。那么docker无法拉取镜像只能说明虚拟机中的代理在docker中失效，所以这么需要给Docker也来配置HTTP代理以让 Docker 能正常下载镜像。配置的 HTTP代理主要用于 Docker 拉取 (pull) 和推送 (push) 镜像使用，不会影响 Docker 容器的联网状态。

**创建服务目录**

docker pull命令是由守护进程dockerd来执行，代理需要配在dockerd的环境中。而这个环境受systemd所管控，所以需要在systemd中进行代理配置。执行命令：mkdir -p /etc/systemd/system/docker.service.d在systemd目录下docker.service.d服务目录，该目录用于存放用户的个性设置(默认不存在)，创建后会自动读取相关的设置并自动更新覆盖原有的设置。

```shell
mkdir -p /etc/systemd/system/docker.service.d
```

**创建配置文件**

在刚才创建的 /etc/systemd/system/docker.service.d/ 目录下创建一个 \*.conf 的的配置文件，这里我们是配置代理就直接定义一个 proxy.conf 配置文件来添加代理信息。将 proxy.example.com:8080 换成自己的代理地址，这个代理必须是HTTP代理协议。如果还有内部不需要使用代理来访问则可以添加NO_PROXY配置来跳过代理的地址，填入所用到的本地地址，多个地址用逗号隔开(支持通配符*)。

```shell
vim /etc/systemd/system/docker.service.d/proxy.conf

[Service]
Environment="HTTP_PROXY=http://squid:s5uEnN02Duh9u*rs@10.160.224.134:3128"
Environment="HTTPS_PROXY=http://squid:s5uEnN02Duh9u*rs@10.160.224.134:3128"
Environment="NO_PROXY=localhost,127.0.0.1"
```

**刷新配置**

更新了配置之后我们需要重新读取配置使其生效，然后执行命令：systemctl restart docker来重启docker服务。刷新之后我们可以查通过命令：systemctl show --property=Environment docker看下配置信息，如果在窗口显示了我们刚刚配置的代理信息就说明配置成功了。

```shell
systemctl daemon-reload
systemctl restart docker
systemctl show --property=Environment docker
```

**验证配置是否生效**

最后我们来pull下镜像看看效果，这里我们从Docker Hub上拉取一个最简单的hello-world镜像。这里我们可以看到已经车成功拉取了hello-world镜像，如果不确定可以通过命令：docker images或者docker image ls来查看本机已有的镜像。

**总结：**

这个一般出现在局域网内，如果遇到无法pull镜像可以尝试下配置docker代理。