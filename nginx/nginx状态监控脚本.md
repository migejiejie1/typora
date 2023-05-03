监控逻辑为：监控nginx端口状态是否正常 以及 nginx进程号是否存在
监控脚本名称为 nginx_monitor.sh，脚本内容如下

```shell
#!/bin/bash

MONITOR_LOG=/opt/logs/nginx_monitor.log 
NGINX_CMD=/data/nginx/sbin/nginx

nginx_monitor()
{
	#nginx的端口号
	PORT="80"

	#获取nginx端口监听状态，如果nginx正常运行，PORT_FLAG值为0
	PORT_STATUS=$(netstat -plnt|grep -i listen|grep $1|grep -w ${PORT})

	#获取上一个命令的退出状态，正常退出为0
	PORT_FLAG=$?

	#使用管道命令查询nginx的进程号
	NGINX_STATUS=$(ps -ef |grep $1|grep -v 'grep'|grep master|awk '{print $2}')

	#如果进程号存在并且端口监听正常，则说明nginx正常运行
	if [ ${NGINX_STATUS} ] && [ ${PORT_FLAG} = "0" ];then
	   echo "$(date +%Y/%m/%d-%H:%M:%S) $1 is running" >>${MONITOR_LOG} 2>&1
	else  #否则说明nginx存在异常,尝试重新启动
	   echo "$(date +%Y/%m/%d-%H:%M:%S) [error] $1 has already shutdown" >>${MONITOR_LOG} 2>&1
	   killall $1 &> /dev/null
	   $NGINX_CMD
	fi
}

#调用nginx_monitor函数，其中nginx是参数，为nginx的进程名，也就是nginx的目录名
nginx_monitor nginx

```

手动执行脚本使用如下命令, 需要先获得执行权限

```shell
# chmo +x nginx_monitor.sh
# sudo bash nginx_monitor.sh
```

如果想要定时执行，需要使用linux的crontab，使用 sudo crontab -e 命令打开配置文件，在文件末尾添加如下内容

```shell
# sudo crontab -e 
*/1 * * * * bash /opt/monitor_nginx.sh
```

其中
1）*/1 * * * *表示每分钟执行一次
2）bash表示使用bash来执行该脚本
3）/opt/monitor_nginx.sh 为监控脚本的路径，需要根据自己实际情况来配置