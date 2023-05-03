Docker的三种网络代理配置
有时因为网络原因，比如公司NAT，或其它啥的，需要使用代理。 Docker的代理配置，略显复杂，因为有三种场景。 但基本原理都是一致的，都是利用Linux的http_proxy等环境变量。

========= dockerd代理 =========
在执行docker pull时，是由守护进程dockerd来执行。 因此，代理需要配在dockerd的环境中。 而这个环境，则是受systemd所管控，因此实际是systemd的配置。

```shell
sudo mkdir -p /etc/systemd/system/docker.service.d
sudo touch /etc/systemd/system/docker.service.d/proxy.conf
```

在这个proxy.conf文件（可以是任意*.conf的形式）中，添加以下内容：

```shell
[Service]
Environment="HTTP_PROXY=http://proxy.example.com:8080/"
Environment="HTTPS_PROXY=http://proxy.example.com:8080/"
Environment="NO_PROXY=localhost,127.0.0.1,.example.com"


[Service]
Environment="HTTP_PROXY=http://squid:s5uEnN02Duh9u*rs@10.160.224.134:3128"
Environment="HTTPS_PROXY=http://squid:s5uEnN02Duh9u*rs@10.160.224.134:3128"
Environment="NO_PROXY=localhost,127.0.0.1"

```

其中，proxy.example.com:8080要换成可用的免密代理。 通常使用cntlm在本机自建免密代理，去对接公司的代理。 可参考《Linux下安装配置Cntlm代理》。

========= Container代理 =========
在容器运行阶段，如果需要代理上网，则需要配置~/.docker/config.json。 以下配置，只在Docker 17.07及以上版本生效。

```shell
{
 "proxies":
 {
   "default":
   {
     "httpProxy": "http://proxy.example.com:8080",
     "httpsProxy": "http://proxy.example.com:8080",
     "noProxy": "localhost,127.0.0.1,.example.com"
   }
 }
}
```

这个是用户级的配置，除了proxies，docker login等相关信息也会在其中。 而且还可以配置信息展示的格式、插件参数等。

此外，容器的网络代理，也可以直接在其运行时通过-e注入http_proxy等环境变量。 这两种方法分别适合不同场景。 config.json非常方便，默认在所有配置修改后启动的容器生效，适合个人开发环境。 在CI/CD的自动构建环境、或者实际上线运行的环境中，这种方法就不太合适，用-e注入这种显式配置会更好，减轻对构建、部署环境的依赖。 当然，在这些环境中，最好用良好的设计避免配置代理上网。

========= docker build代理 =========
虽然docker build的本质，也是启动一个容器，但是环境会略有不同，用户级配置无效。 在构建时，需要注入http_proxy等参数。

```shell
docker build . \
    --build-arg "HTTP_PROXY=http://proxy.example.com:8080/" \
    --build-arg "HTTPS_PROXY=http://proxy.example.com:8080/" \
    --build-arg "NO_PROXY=localhost,127.0.0.1,.example.com" \
    -t your/image:tag
```

注意：无论是docker run还是docker build，默认是网络隔绝的。 如果代理使用的是localhost:3128这类，则会无效。 这类仅限本地的代理，必须加上--network host才能正常使用。 而一般则需要配置代理的外部IP，而且代理本身要开启gateway模式。

========= 重启生效 =========
代理配置完成后，reboot重启当然可以生效，但不重启也行。

docker build代理是在执行前设置的，所以修改后，下次执行立即生效。 Container代理的修改也是立即生效的，但是只针对以后启动的Container，对已经启动的Container无效。

dockerd代理的修改比较特殊，它实际上是改systemd的配置，因此需要重载systemd并重启dockerd才能生效。刷新之后我们可以查通过命令：systemctl show --property=Environment docker看下配置信息，如果在窗口显示了我们刚刚配置的代理信息就说明配置成功了。

```shell
sudo systemctl daemon-reload
sudo systemctl restart docker
systemctl show --property=Environment docker
```

