**sshd启动报错 no hostkeys available -- exiting**

尝试启动ssh服务

```shell
service ssh start
```

结果报错个问题，提示sshd: no hostkeys available – exiting.

解决办法

```shell
# ssh-keygen -A
# /etc/init.d/ssh start
```

