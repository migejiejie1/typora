```shell
nginx和php-fpm的关系

正向代理翻墙访问谷歌

对于人来说可以感知到，但服务器感知不到，我们叫他正向代理服务器。

 

反向代理访问百度 中间有个负载代理服务器

对于人来说不可感知，但对于服务器来说是可以感知的，我们叫他反向代理服务器

 

而nginx就是一个高性能的http和反向代理服务器，也是一个IMAP/POP3/SMTP服务器

 

Php-fpm：php-Fastcgi Process Manager. PHP的FastCGI进程管理器

 

要想知道什么是PHP-fpm，首先要知道什么是fastcgi，更要知道什么是cgi

 

Cgi 通用网关接口，Web 服务器运行时外部程序（php,python）的规范

 

Web服务器早期只处理html静态文件，当php等动态语言出现后处理不了，就需要使用各个动态语言的解释器，那么解释器和web服务器之间的通信标准或者协议或者接口就是cgi。每次php文件的访问都会复刻出一个进程进行处理

 

Fastcgi 常驻型的cgi，只要激活后，一直执行，不会每次都要花费时间去fork一次(这是CGI最为人诟病的fork-and-execute 模式)。

 

php-fpm是 FastCGI 的实现，并提供了进程管理的功能。
进程包含 master 进程和 worker 进程两种进程。
master 进程只有一个，负责监听端口，接收来自 Web Server 的请求，而 worker 进程则一般有多个(具体数量根据实际需要配置)，每个进程内部都嵌入了一个 PHP 解释器，是 PHP 代码真正执行的地方。

还有如果在PHP-FPM管理下的某个FastCGI进程挂了，PHP-FPM会根据用户配置来看是否要重启补全，PHP-FPM更像是管理器，而真正衔接Nginx与PHP的则是FastCGI进程

 

fastcgi_pass所配置的内容，便是告诉Nginx你接收到用户请求以后，你该往哪里转发

 




 

 

 

那么nginx和php-fpm到底是怎么工作的呢。请听下回分解。

 

 

 

 

 

上面通过nginx定义我们知道，Nginx不只有处理http请求的功能，还能做反向代理。

下面是我画的一个简单的流程图

 

 

1. 客户端通过http协议访问网站域名

2. Nginx接受请求，并路由转到index.php

3. 加载fastcgi模块

4. Fastcgi监听127.0.0.1:9000地址

5. 然后访问到达127.0.0.1:9000

6. php-fpm监听127.0.0.1:9000，也就接收到了请求

7. Fpm的master主进程，分配子进程去处理请求

8. 处理完成后，返回结果给nginx

9. Nginx通过http协议响应给客户端

 

 

Nginx负责承载HTTP请求的响应与返回，以及超时控制记录日志等HTTP相关的功能，而PHP则负责处理具体请求要做的业务逻辑，它们俩的这种合作模式也是常见的分层架构设计中的一种，在它们各有专注面的同时，FastCGI又很好的将两块衔接，保障上下游通信交互

 

 

查看fpm进程，可分为主进程和子进程

ps -ef | grep fpm


```

