# **1.** ***\*Linux系统发送系统syslog日志\****

Linux系统可以主动发送syslog日志，CentOS6以后的版本中在/etc/rsyslog.conf配置文件中设置发送内容，地址以及使用端口。

​	![img](Image/wps1-16653882365521.jpg)

截图中左侧为记录的何种日志，对应的右侧的/var/log文件夹下的*.log文件为记录该种日志的文件名。

若要对应发送指定的syslog，如：想将messages里面的信息通过syslog外发就在文件里面增加一行：

 *.* @xxx.xxx.xxx.xxx

​	不写端口，默认使用udp514端口。可修改端口：

\1. udp端口：@xxx.xxx.xxx.xxx：udp端口号

\2. tcp端口：@@xxx.xxx.xxx.xxx：tcp端口号

 

修改完成后需要重启rsyslog服务：

1.在CentOS7及以后的最新版本是使用systemctl命令，具体指令：

systemctl restart rsyslog.service   //重启rsyslog服务

systemctl status rsyslog.service   //查看rsyslog状态

 

在CentOS6.*版本，指令如下：

service rsyslog restart 



 

# **2.** ***\*Linux中间件发送日志（使用脚本）\****

## 	***\*脚本原理说明\****

​	对于安装在Linux系统下的中间件，其日志也可通过脚本发送。脚本主要部分截图如下：

​	![img](Image/wps2-16653882365532.jpg)

​	该脚本用于发送指定文件夹下的多个日志文件。

​	如注释所写，需要修改以下几部分内容：

\1. 监控文件目录

\2. 转发的目的地址

\3. 使用的udp端口号

\4. 文件过滤规则：上图脚本是发送weblogic的*.out文件，需要发送的*.out日志文件如下图：

![img](Image/wps3-16653882365533.png) 

\5. 是否转发历史数据：默认不转发，脚本执行后只转发执行后生成的新日志，yes的话会将文件夹下所有符合格式的日志文件全部发送一遍，再发送脚本执行后只转发执行后生成的新日志。

 

## ***\*脚本使用说明\****

\1. 将脚本复制到相应的Linux服务器，推荐所有脚本放在统一的文件夹中，可一并放到/var/log这个系统日志文件夹中

\2. 对.sh脚本设置755权限

\3. 使用nohup指令，使得脚本在后台一直执行

例如：nohup /var/log/filemonitor_weblogic.sh >/dev/null&

\4. 目标服务器检查是否收到日志，若没有，检查脚本设置是否有问题，权限有没有加，防火墙有没有添加策略

\5. 在/etc/rc.local文件中，最后添加nohup指令，使得开机执行此脚本

\6. 若一台服务器有多个中间件或需要发送多个文件夹内的日志文件，可以使用多个脚本，脚本名称不同即可

 

 