```shell
1、概述
有时需要将json和yaml格式的配置文件进行相互转换，那么在linux的环境下，yq就是一个很好的命令行的工具。

2、yq命令安装
通过以下的命令安装yq命令
wget https://github.com/mikefarah/yq/releases/latest/download/yq_linux_amd64 -O /usr/bin/yq \
  && chmod +x /usr/bin/yq
  
安装之后，输入以下的命令，确认yq已经正确的完整安装：
[root@local ~]# yq --version
yq (https://github.com/mikefarah/yq/) version 4.16.2

3、yq命令的使用
3.1、yaml转换为json
yq eval -o json initnginx.yaml |tee initnginx.json  #最后一个是yaml文件的名字。

3.2、json转换为yaml
yq eval -P initnginx.json | tee initnginx.yml
```

