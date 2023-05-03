```shell
systemctl命令列出所有服务

systemctl命令列出所有服务
systemctl是Systemd 的主命令，可用于管理系统。

列出所有已经加载的systemd units

systemctl
systemctl | grep docker.service

列出所有service

systemctl list-units --type=service
systemctl --type=service

列出所有active状态（运行或退出）的服务

systemctl list-units --type=service --state=active

列出所有正在运行的服务

systemctl list-units --type=service --state=running

列出所有正在运行或failed状态的服务

systemctl list-units --type service --state running,failed

列出所有enabled状态的服务

systemctl list-unit-files --state=enabled
————————————————
```

