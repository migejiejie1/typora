```shell
#!/bin/bash
# chkconfig: 35 90 15
# author:      Amber
# version:     1.0.0
# date:        2018-10-10
# description: nginx server control shell scripts

NGINX=/usr/local/nginx/sbin/nginx
OK="\033[32m成功\033[0m"
ERR="\033[31m失败\033[0m"

nginx_start()
{
    PORT=`netstat -anpt |grep :80.*LISTEN.*nginx |wc -l`
    if [ $PORT -eq 0 ];then
        $NGINX
        echo -e "Nginx is starting...           [$OK]"
    else
        echo -e "Error: Nginx is already running...        [$ERR]"
    fi
}
nginx_stop()
{
    PORT=`netstat -anpt |grep :80.*LISTEN.*nginx |wc -l`
    if [ $PORT -eq 0 ];then
        echo -e "Error: Nginx has already stopped...       [$ERR]"
    else
        $NGINX -s stop
        echo -e "Nginx is stopping...                    [$OK]"
    fi
}
nginx_restart()
{
    nginx_stop
    sleep 1
    nginx_start
}
nginx_reload()
{
    PORT=`netstat -anpt |grep :80.*LISTEN.*nginx |wc -l`
    if [ $PORT -eq 0 ];then
        echo -e "Error: Nginx can't reload. Nginx has already stopped...                        [$ERR]"
    else
        $NGINX -s reload
        echo -e "Nginx is reloading...                                      [$OK]"
    fi
}
nginx_status()
{
    PORT=`netstat -anpt |grep :80.*LISTEN.*nginx |wc -l`
    if [ $PORT -eq 0 ];then
        echo "Nginx has already stopped."
    else
        echo "Nginx is already running."
    fi
}

main()
{
case $1 in
    start)
        nginx_start;;
    stop)
        nginx_stop;;
    restart)
        nginx_restart;;
    reload)
        nginx_reload;;
    status)
        nginx_status;;
    *)
        echo "Usage: nginx {start|stop|restart|reload|status}"
esac
}

main $1
```

