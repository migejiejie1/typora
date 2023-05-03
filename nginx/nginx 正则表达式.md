```
1、nginx配置基础

1、正则表达式匹配

~ 区分大小写匹配

~* 不区分大小写匹配

!~和!~*分别为区分大小写不匹配及不区分大小写不匹配

^ 以什么开头的匹配

$ 以什么结尾的匹配

转义字符。可以转. * ?等

* 代表任意字符

2、文件及目录匹配

-f和!-f用来判断是否存在文件

-d和!-d用来判断是否存在目录

-e和!-e用来判断是否存在文件或目录

-x和!-x用来判断文件是否可执行

例:

location = /

#匹配任何查询，因为所有请求都已 / 开头。但是正则表达式规则和长的块规则将被优先和查询匹配

location ^~ /images/ {

# 匹配任何已/images/开头的任何查询并且停止搜索。任何正则表达式将不会被测试。

location ~* .(gif|jpg|jpeg)$ {

# 匹配任何已.gif、.jpg 或 .jpeg 结尾的请求

入门

1、if指令
所有的Nginx内置变量都可以通过if指令和正则表达式来进行匹配，并且根据匹配结果进行一些操作，如下：

 代码如下	复制代码
if ($http_user_agent ~ MSIE) {
  rewrite  ^(.*)$  /msie/$1  break;
}
 
if ($http_cookie ~* "id=([^;] +)(?:;|$)" ) {
  set  $id  $1;
}
使用符号~*和~模式匹配的正则表达式：

1.~为区分大小写的匹配。
2.~*不区分大小写的匹配（匹配firefox的正则同时匹配FireFox）。
3.!~和!~*意为“不匹配的”。
Nginx在很多模块中都有内置的变量，常用的内置变量在HTTP核心模块中，这些变量都可以使用正则表达式进行匹配。

2、可以通过正则表达式匹配的指令
location
查看维基：location
可能这个指令是我们平时使用正则匹配用的最多的指令：

 代码如下	复制代码
location ~ .*.php?$ {
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  /data/wwwsite/test.com/$fastcgi_script_name;
        include        fcgi.conf;
    }

几乎每个基于LEMP的主机都会有如上一段代码。他的匹配规则类似于if指令，不过他多了三个标识符，^~、=、@。并

且它没有取反运算符!，这三个标识符的作用分别是：

1.^~ 标识符后面跟一个字符串。Nginx将在这个字符串匹配后停止进行正则表达式的匹配（location指令中正则表达

式的匹配的结果优先使用），如：location ^~ /images/，你希望对/images/这个目录进行一些特别的操作，如增加

expires头，防盗链等，但是你又想把除了这个目录的图片外的所有图片只进行增加expires头的操作，这个操作可能

会用到另外一个location，例如：location ~* .(gif|jpg|jpeg)$，这样，如果有请求/images/1.jpg，nginx如何决

定去进行哪个location中的操作呢？结果取决于标识符^~，如果你这样写：location /images/，这样nginx会将1.jpg

匹配到location ~* .(gif|jpg|jpeg)$这个location中，这并不是你需要的结果，而增加了^~这个标识符后，它在匹

配了/images/这个字符串后就停止搜索其它带正则的location。
2.= 表示精确的查找地址，如location = /它只会匹配uri为/的请求，如果请求为/index.html，将查找另外的

location，而不会匹配这个，当然可以写两个location，location = /和location /，这样/index.html将匹配到后者

，如果你的站点对/的请求量较大，可以使用这个方法来加快请求的响应速度。
3.@ 表示为一个location进行命名，即自定义一个location，这个location不能被外界所访问，只能用于Nginx产生的

子请求，主要为error_page和try_files。
注意，这3个标识符后面不能跟正则表达式，虽然配置文件检查会通过，而且没有任何警告，但是他们并不会进行匹配

。
综上所述，location指令对于后面值的匹配顺序为：

1.标识符“=”的location会最先进行匹配，如果请求uri匹配这个location，将对请求使用这个location的配置。
2.进行字符串匹配，如果匹配到的location有^~这个标识符，匹配停止返回这个location的配置。
3.按照配置文件中定义的顺序进行正则表达式匹配。最早匹配的location将返回里面的配置。
4.如果正则表达式能够匹配到请求的uri，将使用这个正则对应的location，如果没有，则使用第二条匹配的结果。
server_name
查看维基：server_name
server_name用于配置基于域名或IP的虚拟主机，这个指令也是可以使用正则表达式的，但是注意，这个指令中的正则

表达式不用带任何的标识符，但是必须以~开头：

 代码如下	复制代码
server {
  server_name   www.example.com   ~^wwwd+.example.com$;
}
server_name指令中的正则表达式可以使用引用，高级的应用可以查看这篇文章：在server_name中使用正则表达式

fastcgi_split_path_info
查看维基：fastcgi_split_path_info
这个指令按照CGI标准来设置SCRIPT_FILENAME (SCRIPT_NAME)和PATH_INFO变量，它是一个被分割成两部分（两个引用

）的正则表达式。如下：

 

 代码如下	复制代码
location ~ ^.+.php {
  (...)
  fastcgi_split_path_info ^(.+.php)(.*)$;
  fastcgi_param SCRIPT_FILENAME /path/to/php$fastcgi_script_name;
  fastcgi_param PATH_INFO $fastcgi_path_info;
  fastcgi_param PATH_TRANSLATED $document_root$fastcgi_path_info;
  (...)
}
第一个引用（.+.php）加上/path/to/php将作为SCRIPT_FILENAME，第二个引用(.*)为PATH_INFO，例如请求的完整

URI为show.php/article/0001，则上例中SCRIPT_FILENAME的值为/path/to/php/show.php，PATH_INFO则

为/article/0001。
这个指令通常用于一些通过PATH_INFO美化URI的框架（例如CodeIgniter）。

gzip_disable
查看维基：gzip_disable
通过正则表达式来指定在哪些浏览器中禁用gzip压缩。


gzip_disable     "msie6";rewrite
查看维基：rewrite
这个指令应该也是用的比较多的，它需要使用完整的包含引用的正则表达式：

 代码如下	复制代码
rewrite  "/photos/([0-9] {2})([0-9] {2})([0-9] {2})" /path/to/photos/$1/$1$2/$1$2$3.png;通常环境下我们

会把它和if结合来使用：

 代码如下	复制代码
if ($host ~* www.(.*)) {
  set $host_without_www $1;
  rewrite ^(.*)$ http://$host_without_www$1 permanent; # $1为'/foo'，而不是'www.mydomain.com/foo'
}

Nginx中的正则如何匹配中文
首先确定在编译pcre时加了enable-utf8参数，如果没有，请重新编译pcre，然后就可以在Nginx的配置文件中使用这

样的正则：”(*UTF8)^/[x{4e00}-x{9fbf}]+)$”注意引号和前面的(*UTF8)，(*UTF8)将告诉这个正则切换为UTF8模

式。

 代码如下	复制代码
[root@backup conf]# pcretest
PCRE version 8.10 2010-06-25

  re> /^[x{4e00}-x{9fbf}]+/8
data> 测试
 0: x{6d4b}x{8bd5}
data> Nginx模块参考手册中文版
No match
data> 参考手册中文版
 0: x{53c2}x{8003}x{624b}x{518c}x{4e2d}x{6587}x{7248}

location顺序错误导致下载.php源码而不执行php程序的问题

看下面的例子片断(server段、wordpress安装到多个目录)： 
=====================================

 代码如下	复制代码
location / { 
        try_files $uri $uri/ /index.html; 
}

location /user1/ { 
      try_files $uri $uri/ /user1/index.php?q=$uri&$args; 
}

location ~* ^/(user2|user3)/ { 
        try_files $uri $uri/ /$1/index.php?q=$uri&$args; 
}

location ~ .php$ { 
        fastcgi_pass 127.0.0.1:9000; 
        fastcgi_index index.php; 
        include fastcgi_params; 
}

=====================================

nginx.conf的配置代码看上去没有任何问题，而事实上： 
访问 /user1/会正常执行php程序。 
访问 /user2/ 或 /user3/ 都不会执行程序，而是直接下载程序的源代码。

原因在哪里？看到他们地区别了吗？ 
/user1/是普通location写法 
而/user2/ 或 /user3/ 是正则表达式匹配的location

问题就出在了/user2/ 或 /user3/匹配location指令使用了正则表达式，所以必须注意代码段的先后顺序，必须把

location ~ .php$ {...}段上移、放到它的前面去。

正确的代码举例： 
=====================================

 代码如下	复制代码
location / { 
        try_files $uri $uri/ /index.html; 
}

location /user1/ { 
      try_files $uri $uri/ /user1/index.php?q=$uri&$args; 
}

location ~ .php$ { 
        fastcgi_pass 127.0.0.1:9000; 
        fastcgi_index index.php; 
        include fastcgi_params; 
}

location ~* ^/(user2|user3)/ { 
        try_files $uri $uri/ /$1/index.php?q=$uri&$args; 
}

=====================================

【注意】对于普通location指令行，是没有任何顺序的要求的。如果你也遇到了类似的问题，可以尝试调整使用正则

表达式的location指令片断的顺序来调试
```