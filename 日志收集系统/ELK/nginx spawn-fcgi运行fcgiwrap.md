```shell
spawn-fcgi运行fcgiwrap

1. 下载spawn-fcgi并安装

1.1 http://download.lighttpd.net/spawn-fcgi/releases-1.6.x/spawn-fcgi-1.6.3.tar.gz -P /usr/local/src
1.2 tar zxvf /usr/local/src/spawn-fcgi-1.6.3.tar.gz -P /usr/local/src  
1.3 cd /usr/local/src/spawn-fcgi-1.6.3  
1.4 ./autogen.sh
1.5 ./configure
1.6 make
1.7 make install

自动产生
/usr/local/bin/spawn-fcgi


2. 下载并安装fcgi库

2.1 wget http://fastcgi.com/dist/fcgi-2.4.0.tar.gz -P /usr/local/src  
2.2 tar zxvf /usr/local/src/fcgi-2.4.0.tar.gz -C /usr/ocal/src  
2.3 cd /usr/local/src/fcgi-2.4.0  
2.4 ./configure  
2.5 make  
2.6 make install 

或通过yum安装fcgi库

yum install -y git-core autoconf automake fcgi fcgi-devel make unzip

3. 安装fcgiwrap

3.1 wget https://download.github.com/gnosek-fcgiwrap-1.0.3-4-g58ec209.tar.gz -P /usr/locl/src  
3.2 tar zxvf /usr/local/src/gnosek-fcgiwrap-1.0.3-4-g58ec209.tar.gz -P /usr/local/src  
3.3 cd /usr/local/src/gnosek-fcgiwrap-58ec209  
3.4 autoreconf -i
3.5 ./configure
3.6 make
3.7 make install

3.8 自动产生
/usr/local/sbin/fcgiwrap 

3.9 创建启动用户
useradd nginx

3.10 socket文件路径
/tmp/fcgiwrap.socket

3.11启动fcgiwrap服务
/etc/init.d/fcgiwrap start 

3.12 启动脚本如下
[root@linux ~]# cat /etc/init.d/fcgiwrap 
#! /bin/bash
### BEGIN INIT INFO
# Provides:          fcgiwrap
# Required-Start:    $remote_fs
# Required-Stop:     $remote_fs
# Should-Start:
# Should-Stop:
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: FastCGI wrapper
# Description:       Simple server for running CGI applications over FastCGI
### END INIT INFO

PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
SPAWN_FCGI="/usr/local/bin/spawn-fcgi"
DAEMON="/usr/local/sbin/fcgiwrap"
NAME="fcgiwrap"

PIDFILE="/var/run/$NAME.pid"

FCGI_SOCKET="/tmp/$NAME.socket"
FCGI_USER="nginx"
FCGI_GROUP="nginx"
FORK_NUM=5
SCRIPTNAME=/etc/init.d/$NAME

case "$1" in
    start)
        echo -n "Starting $NAME... "

        PID=`pidof $NAME`
        if [ ! -z "$PID" ]; then
            echo " $NAME already running"
            exit 1
        fi

        $SPAWN_FCGI -u $FCGI_USER -g $FCGI_GROUP -s $FCGI_SOCKET -P $PIDFILE -F $FORK_NUM -f $DAEMON

        if [ "$?" != 0 ]; then
            echo " failed"
            exit 1
        else
            echo " done"
        fi
    ;;

    stop)
        echo -n "Stoping $NAME... "

        PID=`pidof $NAME`
        if [ ! -z "$PID" ]; then
            kill `pidof $NAME`
            if [ "$?" != 0 ]; then
                echo " failed. re-quit"
                exit 1
            else
                rm -f $pid
                echo " done"
            fi
        else
            echo "$NAME is not running."
            exit 1
        fi
    ;;

    status)
        PID=`pidof $NAME`
        if [ ! -z "$PID" ]; then
            echo "$NAME (pid $PID) is running..."
        else
            echo "$NAME is stopped"
            exit 0
        fi
    ;;

    restart)
        $SCRIPTNAME stop
        sleep 1
        $SCRIPTNAME start
    ;;

    *)
        echo "Usage: $SCRIPTNAME {start|stop|restart|status}"
        exit 1
    ;;
esac

3.13 添加为服务
chmod 0755 /etc/rc.d/init.d/fcgiwrap  
chown root:root /etc/rc.d/init.d/fcgiwrap  
chkconfig –add fcgiwrap  
chkconfig fcgiwrap on 


4. NGINX编译安装

4.1 安装依赖包
yum install gd gd-devel gcc gcc-c++ make  GeoIP-dev* 
yum -y install jq
 
4.2 编译安装 
./configure --prefix=/data/nginx --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --http-client-body-temp-path=/data/nginx/client_temp --http-proxy-temp-path=/data/nginx/proxy_temp --http-fastcgi-temp-path=/data/nginx/fastcgi_temp --http-uwsgi-temp-path=/data/nginx/uwsgi_temp --http-scgi-temp-path=/data/nginx/scgi_temp --user=nginx --group=nginx --with-mail --with-stream --with-threads --with-file-aio --with-poll_module --with-select_module --with-http_v2_module --with-http_flv_module --with-http_mp4_module --with-http_sub_module --with-http_dav_module --with-http_flv_module --with-http_ssl_module --with-http_geoip_module --with-http_slice_module --with-http_gunzip_module --with-http_realip_module --with-http_addition_module --with-http_image_filter_module --with-http_gzip_static_module --with-http_random_index_module --with-http_secure_link_module --with-http_degradation_module --with-http_stub_status_module --with-mail_ssl_module --with-stream_ssl_module --with-stream_realip_module --with-stream_ssl_preread_module --with-pcre=/data/src/pcre-8.35  --with-openssl=/data/src/openssl-1.0.2h --with-zlib=/data/src/zlib-1.2.8 

make && make install 

4.3 启动NGINX
./nginx
```

