```shell
#!/bin/bash
#
# chkconfig: 35 80 15
# description: tomcat启停脚本
# author: Amber(qq:724639089)
# version: 1.0.0
# date: 2018-08-21

PORT=8080

tomcat_start() {
    STATUS=`netstat -ant |grep :$PORT.*LISTEN|wc -l`
    if [ $STATUS -eq 0 ];then
        /usr/local/tomcat7/bin/startup.sh
    else
        echo "Error: Tomcat is already running!"
    fi
}
tomcat_stop() {
    STATUS=`netstat -ant |grep :$PORT.*LISTEN|wc -l`
    if [ $STATUS -ge 1 ];then
        /usr/local/tomcat7/bin/shutdown.sh
        rm -rf /usr/local/tomcat7/work/*
        rm -rf /usr/local/tomcat7/temp/*
    else
        echo "Error: Tomcat is stopped."
    fi
}
tomcat_status() {
    STATUS=`netstat -ant |grep :$PORT.*LISTEN|wc -l`
    if [ $STATUS -ge 1 ];then
        echo "Tomcat is running."
    else
        echo "Tomcat is stopped."
    fi
}

case $1 in
start)
    tomcat_start;;
stop)
    tomcat_stop;;
restart)
    tomcat_stop && sleep 2 && tomcat_start;;
status)
    tomcat_status;;
*)
    echo "Usage: tomcat (start|stop|restart|status)"
esac
```

