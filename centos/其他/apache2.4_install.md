```shell
三、	CentOS 7环境源码安装LAMP
1、安装Apache
（1）安装依赖包
[root@lamp ~]# yum install gcc gcc-c++ make pcre-devel expat-devel perl wget vim -y

（2）安装apr apr-util

知识补充：
APR(Apache portable Run-time libraries，Apache可移植运行库)，主要为上层的应用程序提供一个可以跨越多操作系统平台使用的底层支持接口库。在早期 的Apache版本中，应用程序本身必须能够处理各种具体操作系统平台的细节，并针对不同的平台调用不同的处理函数。
在早期的Apache版本中，应用程序本身必须能够处理各种具体操作系统平台的细节，并针对不同的平台调用不同的处理函数。随着Apache的进一步开发，Apache组织决定将这些通用的函数独立出来并发展成为一个新的项目。这样，APR的开发就从Apache中独立出来，Apache仅仅是使用APR而已。
一般情况下，APR开发包很容易理解为仅仅是一个开发包，不过事实上并不是。目前，完整的APR实际上包含了三个开发包：apr、apr-util以及apr-iconv，每一个开发包分别独立开发，并拥有自己的版本。
apr中包含了一些通用的开发组件，包括mmap，DSO等等
apr-util该目录中也是包含了一些常用的开发组件。这些组件与apr目录下的相比，它们与apache的关系更加密切一些。比如存储段和存储段组，加密等等。



[root@lamp ~]# tar xf apr-1.6.5.tar.gz -C /usr/src/
[root@lamp ~]# tar xf apr-util-1.6.1.tar.gz -C /usr/src/
[root@lamp ~]# tar xf httpd-2.4.38.tar.gz -C /usr/src/

[root@lamp ~]# cd /usr/src/
[root@lamp src]# ls
apr-1.6.5  apr-util-1.6.1  debug  httpd-2.4.38  kernels
[root@lamp src]# mv  apr-1.6.5/   httpd-2.4.38/srclib/apr
[root@lamp src]# mv  apr-util-1.6.1/   httpd-2.4.38/srclib/apr-util

（3）编译安装Apache
[root@lamp src]# cd httpd-2.4.38/
[root@lamp httpd-2.4.38]# ./configure --prefix=/usr/local/httpd --enable-charset-lite --enable-rewrite --enable-cgi --enable-so --enable-deflate --enable-expires && make && make install
 
模块简介：
mod_charset_lite		指定字符集转换或重新编码
mod_rewrite			提供一个基于规则的重写引擎来动态重写请求的url
mod_cgi				执行CGI脚本
mod_so				在启动或重新启动时将可执行代码和模块加载到服务器

参考：http://httpd.apache.org/docs/2.4/mod/#C

（4）安装后优化
修改程序用户程序组
[root@lamp ~]# useradd -M -s /sbin/nologin apache
[root@lamp ~]# chown -R apache.apache /usr/local/httpd/
[root@lamp ~]# sed -i 's/User daemon/User apache/' /usr/local/httpd/conf/httpd.conf
[root@lamp ~]# sed -i 's/Group daemon/Group apache/' /usr/local/httpd/conf/httpd.conf

修改servername
[root@lamp ~]# sed -i '/#ServerName/ s/#//' /usr/local/httpd/conf/httpd.conf

增加系统命令变量路径
[root@lamp ~]# echo 'PATH=$PATH:/usr/local/httpd/bin/' >> /etc/profile
[root@lamp ~]# . /etc/profile

创建启动脚本并设置开机自启
[root@lamp ~]# cp /usr/local/httpd/bin/apachectl /etc/init.d/httpd
[root@lamp ~]# sed -i '1a# chkconfig: 35 85 15' /etc/init.d/httpd 
[root@lamp ~]# head -2 /etc/init.d/httpd 
#!/bin/sh
# chkconfig: 35 85 15
[root@lamp ~]# chkconfig --add httpd

[root@lamp ~]# chmod +x /etc/rc.d/rc.local 
[root@lamp ~]# echo "/etc/init.d/httpd start" >> /etc/rc.d/rc.local
启动服务测试
[root@lamp ~]# /etc/init.d/httpd start
[root@lamp ~]# netstat -anpt | grep :80
tcp6       0      0 :::80                   :::*                    LISTEN      64521/httpd  

[root@lamp ~]# curl 192.168.1.66
<html><body><h1>It works!</h1></body></html>

```

