```
Nginx配置server_name

server_name指令

server_name：用来设置虚拟主机服务名称。

127.0.0.1 、 localhost 、域名[www.itcast.cn | www.itheima.com]

语法	server_name name ...; name可以提供多个中间用空格分隔
默认值	server_name "";
位置	server


关于server_name的配置方式有三种，分别是：

·精确匹配

·通配符匹配

·正则表达式匹配


配置方式一：精确匹配

如

server {
    listen 80;
    server_name www.itcast.cn www.itheima.cn;
    ...
}
补充小知识点:

hosts是一个没有扩展名的系统文件，可以用记事本等工具打开，其作用就是将一些常用的网址域名与其对应的IP地址建立一个关联“数据库”，当用户在浏览器中输入一个需要登录的网址时，系统会首先自动从hosts文件中寻找对应的IP地址，一旦找到，系统会立即打开对应网页，如果没有找到，则系统会再将网址提交DNS域名解析服务器进行IP地址的解析。

windows:C:\Windows\System32\drivers\etc

centos：/etc/hosts

因为域名是要收取一定的费用，所以我们可以使用修改hosts文件来制作一些虚拟域名来使用。需要修改 /etc/hosts文件来添加

vim /etc/hosts
127.0.0.1 www.itcast.cn
127.0.0.1 www.itheima.cn

配置方式二:使用通配符配置

server_name中支持通配符"*",但需要注意的是通配符不能出现在域名的中间，只能出现在首段或尾段，如：

server {
    listen 80;
    server_name  *.itcast.cn    www.itheima.*;
    # www.itcast.cn abc.itcast.cn www.itheima.cn www.itheima.com
    ...
}
下面的配置就会报错

server {
    listen 80;
    server_name  www.*.cn www.itheima.c*
    ...
}

配置三:使用正则表达式配置

server_name中可以使用正则表达式，并且使用~作为正则表达式字符串的开始标记。

常见的正则表达式

代码	说明
^	匹配搜索字符串开始位置
$	匹配搜索字符串结束位置
.	匹配除换行符\n之外的任何单个字符
\	转义字符，将下一个字符标记为特殊字符
[xyz]	字符集，与任意一个指定字符匹配
[a-z]	字符范围，匹配指定范围内的任何字符
\w	与以下任意字符匹配 A-Z a-z 0-9 和下划线,等效于[A-Za-z0-9_]
\d	数字字符匹配，等效于[0-9]
{n}	正好匹配n次
{n,}	至少匹配n次
{n,m}	匹配至少n次至多m次
*	零次或多次，等效于{0,}
+	一次或多次，等效于{1,}
?	零次或一次，等效于{0,1}
配置如下：

server{
        listen 80;
        server_name ~^www\.(\w+)\.com$;
        default_type text/plain;
        return 200 $1  $2 ..;
}
注意 ~后面不能加空格，括号可以取值


匹配执行顺序

由于server_name指令支持通配符和正则表达式，因此在包含多个虚拟主机的配置文件中，可能会出现一个名称被多个虚拟主机的server_name匹配成功，当遇到这种情况，当前的请求交给谁来处理呢?

server{
    listen 80;
    server_name ~^www\.\w+\.com$;
    default_type text/plain;
    return 200 'regex_success';
}

server{
    listen 80;
    server_name www.itheima.*;
    default_type text/plain;
    return 200 'wildcard_after_success';
}

server{
    listen 80;
    server_name *.itheima.com;
    default_type text/plain;
    return 200 'wildcard_before_success';
}

server{
    listen 80;
    server_name www.itheima.com;
    default_type text/plain;
    return 200 'exact_success';
}

server{
    listen 80 default_server;
    server_name _;
    default_type text/plain;
    return 444 'default_server not found server';
}
结论：

exact_success
wildcard_before_success
wildcard_after_success
regex_success
default_server not found server!!

No1:准确匹配server_name

No2:通配符在开始时匹配server_name成功

No3:通配符在结束时匹配server_name成功

No4:正则表达式匹配server_name成功

No5:被默认的default_server处理，如果没有指定默认找第一个server
```

