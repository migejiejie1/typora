```shell
nginx开启文件共享功能
如何让nginx显示文件夹目录
vi /etc/nginx/conf.d/default.conf

添加如下内容：
location / {
    alias /data/www/file                      //指定实际目录绝对路径；
    autoindex on;                                  //开启目录浏览功能；
    autoindex_exact_size off;            //关闭详细文件大小统计，让文件大小显示MB，GB单位，默认为b；
    autoindex_localtime on;              //开启以服务器本地时区显示文件修改日期！
}

搭建静态资源web服务器
server {
        listen       8080;                  #使用的端口         
        server_name  localhost;             #使用的域名

        location / {  
            root   /www/nginx;                  #使用资源存放的路径
            index  index.html;                  #主页的文件名
        }
}

nginx -s reload          #重载配置文件


开启文件共享功能

location /pub{                             #在server下新建一个location
    alias /www/nginx/pub/;             #共享文件的路径，分号前加一个/
    autoindex on;                      #开启自动索引功能，将共享目录下的文件整合成页面
}
 
 
#alias与root的区别：
#location /pub {    root   /var/www/html;}  用户实际访问到的路径是  /var/www/html/pub
#location /pub {    alias  /var/www/html/;} 用户实际访问到的路径是  /var/www/html

```

