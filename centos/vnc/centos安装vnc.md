```shell
1.安装 Gnome 包
[root@s10 ~]# yum groupinstall "GNOME Desktop" -y

2.更新系统的运行级别 （此步骤不是必须）
自动进入图形界面，那么我们需要更改系统的运行级别，输入下面的命令来启用图形界面。

[root@s10 ~]# ln -sf /lib/systemd/system/runlevel5.target /etc/systemd/system/default.target

3.安装vnc软件包
[root@s10 ~]# yum -y install tigervnc-server

4.关闭防火墙
[root@s10 ~]# systemctl stop firewalld
[root@s10 ~]# systemctl disable firewalld

5.复制配置文件
[root@s10 ~]# cp /lib/systemd/system/vncserver@.service  /etc/systemd/system/vncserver@:1.service

6.修改配置文件
[root@s10 ~]# vim /etc/systemd/system/vncserver@\:1.service
[Unit]
Description=Remote desktop service (VNC)
After=syslog.target network.target

[Service]
Type=simple

# Clean any existing files in /tmp/.X11-unix environment
ExecStartPre=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'
ExecStart=/usr/bin/vncserver_wrapper root %i
ExecStop=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'

[Install]
WantedBy=multi-user.target

将<USER>修改为root或者你想通过vnc登录的账号

7.重新加载systemd服务
[root@s10 ~]# systemctl daemon-reload

8.设置VNC密码
[root@s10 ~]# vncpasswd

9.启动VNC服务
[root@s10 ~]# systemctl start  vncserver@:1.service
[root@s10 ~]# systemctl enable  vncserver@:1.service
[root@s10 ~]# systemctl status vncserver@:1.service

查看VNC服务端口启动状态：
[root@s10 ~]# netstat  -tunpl |grep  :5901

10.测试连接
```

