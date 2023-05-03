```shell
#!/bin/bash
#TongWeb服务启停脚本
#0	2	*	*	*	每天2点执行

JAVA_SUM=$(ps -ef|grep -i tong |grep -v grep |wc -l)

function start_tongweb()
{
	if [ $JAVA_SUM -eq 0 ]; then
		cd /app/bea/TongWeb5.0-opd/bin/
		nohup ./startserver.sh &
		sleep 2
		cd /app/bea/TongWeb5.0-opd2/bin/
		nohup ./startserver.sh &
	fi	
}

function stop_tongweb()
{
	if  [ $JAVA_SUM -eq 2 ]; then
		#pgrep java |xargs  kill -9 
        ps -ef|grep -i tongweb |grep -v grep|awk '{print $2}' |xargs  kill -9 
	fi
}

function status_tongweb()
{
	if  [ $JAVA_SUM -eq 2 ]; then
		echo 'TongWeb服务已启动'
	else
		echo 'TongWeb服务已关闭'
	fi
}

function main()
{
	case $1 in
		start)
			start_tongweb;;
		stop)
			stop_tongweb;;
		status)
			status_tongweb;;
		restart)
            #stop_tongweb
            #start_tongweb   调用函数重启的话, 需要注意 $JAVA_SUM 的变量值, stop和start时的值是一样的, 需要重新获取该变量的值, 建议使用下面的写法. 
			$0 stop
			sleep 1
			$0 start;;
		*)
			echo "Usage: $0 [start | stop | restart | status]"
	esac
}
 
main $1
```

