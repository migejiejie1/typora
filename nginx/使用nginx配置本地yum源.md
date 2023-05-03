```shell
[root@localhost opt]# ls
nginx-1.20.2.tar.gz

#解压
[root@localhost opt]# tar xf nginx-1.20.2.tar.gz -C /usr/local/
[root@localhost opt]# cd /usr/local/nginx-1.20.2/

#安装编译时需要的依赖包
[root@localhost nginx-1.20.2]#yum -y install pcre-devel zlib-devel gcc gcc-c++ make

#编译安装
[root@localhost nginx-1.20.2]# ./configure --prefix=/usr/local/nginx --user=nginx --group=nginx --with-http_stub_status_module && make && make install

#创建nginx启动用户
[root@localhost nginx-1.20.2]# useradd -M -s /sbin/nologin nginx

#编辑配置文件
[root@localhost nginx]# cd /usr/local/nginx/conf/
[root@localhost conf]# cat nginx.conf
user  nginx;
worker_processes  4;

error_log  logs/error.log  info;

pid        logs/nginx.pid;

events {
    worker_connections  65535;
    use epoll;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  logs/access.log  main;

    sendfile        on;
    tcp_nopush     on;
    tcp_nodelay    on;

    keepalive_timeout  120;
    fastcgi_connect_timeout 300;
    fastcgi_send_timeout 300;
    fastcgi_read_timeout 300;
    fastcgi_buffer_size 64k;
    fastcgi_buffers 4 64k;
    fastcgi_busy_buffers_size 128k;
    fastcgi_temp_file_write_size 128k;

    gzip  on;
    gzip_min_length 1k; 
    gzip_buffers 4 16k;
    gzip_http_version 1.0;
    gzip_comp_level 2; 
    gzip_types text/plain application/x-javascripttext/css application/xml; 
    gzip_vary on;

    server {
        listen       80;
        server_name  localhost;
        charset utf-8;
	#访问准发配置
        location / {
            auth_basic "Kibana"; 
            auth_basic_user_file /etc/nginx/passwd.db;
            proxy_pass http://192.168.1.11:5601;
            proxy_set_header Host $host:5601; 
            proxy_set_header X-Real-IP $remote_addr; 
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; 
            proxy_set_header Via "nginx"; 
        }
	#nginx访问状态配置
        location /status {   
            stub_status on;
            access_log /var/log/nginx/kibana_status.log;
            auth_basic "NginxStatus"; 
        }
	#本地yum源配置
        location /yum {  
	    #root /media/;
	    alias /media/; #需要共享的目录
	    autoindex on;
 	    autoindex_exact_size off;
	    autoindex_localtime on; 
	    index  index.html index.htm;
	}

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
}

#启动nginx, 检查配置文件, 并启动
[root@localhost nginx]# ./sbin/nginx -t 
nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful
[root@localhost nginx]# ./sbin/nginx

```

