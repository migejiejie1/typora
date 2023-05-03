```shell
模块	    作用
command	    执行命令
shell	    执行命令（支持管道符）
yum	        安装软件模块
copy	    配置模块
service	    C6启动模块
systemd	    C7启动模块
user	    用户管理
file	    创建目录、创建文件、往文件写内容
cron	    定时任务
mount	    挂载

yum 模块：
name=名字
state=installed        安装
state=removed       移除
state=latest         更新
举例
ansible oldboy -m yum -a "name=httpd state=installed"

copy模块：
src=本地的文件或目录
dest=远端的文件或目录
dackup=yes    覆盖时需要备份
content=本地复制到远端时能在文件里写内容；content和src只能选一个
group=将本地文件推送到远端，指定文件属组信息
owner=将本地文件推送到远端，指定文件属主信息
举例
推送文件模块
[root@m01 ~]# ansible oldboy -m copy -a "src=/etc/hosts dest=/tmp/test.txt owner=www group=www mode=0600"

在推送覆盖远程端文件前，对远端已有文件进行备份，按照时间信息备份
[root@m01 ~]# ansible oldboy -m copy -a "src=/etc/hosts dest=/tmp/test.txt backup=yes"

直接向远端文件内写入数据信息，并且会覆盖远端文件内原有数据信息
[root@m01 ~]# ansible oldboy -m copy -a "content='bgx' dest=/tmp/oldboy"

模块C6；service     C7；systemd
name=服务的名称
state=started     启动
state=stopped    停止
state=restarted   重启
state=reloaded   平滑重启
enabled=yes   开机自启动
举例
[root@m01 ~]# ansible oldboy -m service -a "name=crond state=stopped enabled=yes"

模块script
举例
编写脚本
[root@m01 ~]# mkdir -p /server/scripts
[root@m01 ~]# cat /server/scripts/yum.sh
#!/usr/bin/bash
yum install -y iftop
在本地运行模块，等同于在远程执行，不需要将脚本文件进行推送目标主机执行
[root@m01 ~]# ansible oldboy -m script -a “/server/scripts/yum.sh”

模块file
path=制定远程主机目录或文件信息
state=link    创建软连接
state=touch   创建文件
state=directory    创建目录
state=absent   删除文件或目录
state=mode    设置文件或目录权限
state=owner   设置文件或目录属主信息
state=group  设置文件或目录属组信息
举例
创建目录
[root@m01 ~]# ansible oldboy -m file -a “path=/tmp/oldboy state=diretory”
创建文件
[root@m01 ~]# ansible oldboy -m file -a “path=/tmp/tt state=touch mode=555 owner=root group=root”
[root@m01 ~]# ansible oldboy -m file -a “src=/tmp/tt path=/tmp/tt_link state=link”

模块group
name=指定创建的组名
gid=指定组的gid
state=absent   移除远端主机的组
state=present   创建远端主机的组
举例
创建组，指定gid
[root@m01 ~]# ansible oldboy -m group -a “name=oldgirl gid=888”

模块user
uid=指定用户的uid
group=指定用户组名称
groups=指定附加组名称
password=给用户加密码
shell=指定用户登录shell
create_home=是否创建家目录
举例
创建oldgirl，设定uid为888，并加入gid为888
[root@m01 ~]# ansible oldboy -m user -a “name=oldgirl uid=888 group=888 shell=/sbin/nologin create_home=no”
随机生成加密字符串（-1使用MD5进行加密 -stdin 非交互式 -salt 加密参数）
[root@m01 ~]# echo “bgx” | openssl passwd -1 -stdin
固定加密字符串
[root@m01 ~]# echo “123”| openssl passwd -1 -stdin -salt ‘salt
创建普通用户，并配置对应的用户密码
[root@m01 ~]# echo “bgx” | openssl passwd -1 -stdin
$1$1KmeCnsK$HGnBE86F/XkXufL.n6sEb.
[root@m01 ~]# ansible oldboy -m user -a ‘name=xlw password=”$1$765yDGau$diDKPRoCIPMU6KEVEaPTZ0″‘

模块crond
name=‘注释’
minute=分
hour=时
day=日
month=月
weekday=周
job=‘操作’
state=absent     删除定时任务
disabled=yes    注释定时任务
举例
正常使用crond服务
[root@m01 ~]# crontab -l
* * * * * /bin/sh /server/scripts/yum.sh
使用ansible添加一条定时任务
[root@m01 ~]# ansible oldboy -m cron -a “minute=* hour=* day=* month=* weekday=* job=’/bin/sh /server/scripts/test.sh'”
[root@m01 ~]# ansible oldboy -m cron -a “job=’/bin/sh /server/scripts/test.sh'”
设置定时任务注释信息，防止重复，name设定
[root@m01 ~]# ansible oldboy -m cron -a “name=’cron01′ job=’/bin/sh /server/scripts/test.sh'”
删除相应定时任务
[root@m01 ~]# ansible oldboy -m cron -a “name=’ansible cron02′ minute=0 hour=0 job=’/bin/sh /server/scripts/test.sh’ state=absent”
注释相应定时任务，使定时任务失效
[root@m01 scripts]# ansible oldboy -m cron -a “name=’ansible cron01′ minute=0 hour=0 job=’/bin/sh /server/scripts/test.sh’ disabled=yes”

模块mount
present=开机挂载
mounted=挂在设备，并将写入/etcfstab
umounted=卸载设备，不会清除/etc/fstab
absent=卸载设备，会清理/etc/fstab
fstype=文件类型
举例
临时挂载设备，并将挂载信息写入/etc/fstab
[root@m01 ~]# ansible web -m mount -a "src=172.16.1.31:/data path=/data fstype=nfs opts=defaults state=mounted"
临时卸载，不会清理/etc/fstab
[root@m01 ~]# ansible web -m mount -a “src=172.16.1.31:/data path=/data fstype=nfs opts=defaults state=unmounted”
卸载，不仅临时卸载，同时会清理/etc/fstab
[root@m01 ~]# ansible web -m mount -a “src=172.16.1.31:/data path=/data fstype=nfs opts=defaults state=absent”

```

