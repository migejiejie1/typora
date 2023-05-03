```shell
Caddy、 SSLDocker、Nginx 性能比较及使用体验
文章目录
Caddy、 SSLDocker、Nginx 并发测试结果
Caddy 并发请求测试结果
SSLDocker 并发请求测试结果
Nginx （openresty）并发请求测试结果
配置文件
Caddy 配置
SSLDocker 配置
Nginx 配置
总结
Caddy、 SSLDocker、Nginx 都是可以用来做前端代理的服务，前两者是用go来写，部署比较简单。

Nginx 在部署HTTPS 时比较麻烦（相对其它两者来说），Caddy、 SSLDocker 都是自动配置并且更新HTTPS，这对我这样的懒人来说很有用。个人一直用Nginx (openresty) 1，后来在go 的世界发现了Caddy 4名器，然后在解决多域名反向代理时发现了SSLDocker 10小神器。

以下是在一台128MB单核的VPS 上部署一个应用，然后分别用Caddy、 SSLDocker、Nginx做前端，反向代理到该应用端口， 在另外一台VPS 做并发请求。开启ssl、gzip，使用hey 1 做并发请求：

# ./hey -n=20000 -c=5 https://mydomain.com/


Caddy、 SSLDocker、Nginx 并发测试结果

Caddy 并发请求测试结果

Summary:
  Total:    64.9214 secs
  Slowest:  0.7156 secs
  Fastest:  0.0031 secs
  Average:  0.0161 secs
  Requests/sec: 308.0650

Response time histogram:
  0.003 [1] |
  0.074 [19888] |∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎
  0.146 [2] |
  0.217 [2] |
  0.288 [2] |
  0.359 [90]    |
  0.431 [1] |
  0.502 [11]    |
  0.573 [1] |
  0.644 [1] |
  0.716 [1] |


SSLDocker 并发请求测试结果

Summary: 
  Total:    63.0618 secs 
  Slowest:  0.4883 secs 
  Fastest:  0.0030 secs 
  Average:  0.0156 secs 
  Requests/sec: 317.1490 

Response time histogram: 
  0.003 [1] | 
  0.052 [19865] |∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎ 
  0.100 [1] | 
  0.149 [2] | 
  0.197 [1] | 
  0.246 [0] | 
  0.294 [15]    | 
  0.343 [95]    | 
  0.391 [0] | 
  0.440 [2] | 
  0.488 [18]    | 


Nginx （openresty）并发请求测试结果

Summary:
  Total:    57.8501 secs
  Slowest:  0.0523 secs
  Fastest:  0.0029 secs
  Average:  0.0144 secs
  Requests/sec: 345.7212

Response time histogram:
  0.003 [1] |
  0.008 [539]   |∎∎
  0.013 [4327]  |∎∎∎∎∎∎∎∎∎∎∎∎∎
  0.018 [13150] |∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎
  0.023 [1397]  |∎∎∎∎
  0.028 [404]   |∎
  0.033 [120]   |
  0.037 [35]    |
  0.042 [18]    |
  0.047 [4] |
  0.052 [5] |


都很稳定，没有失败请求，从 Requests per second (RPS)看：

Caddy 308 < SSLDocker 317 < Nginx 345


三者相差不大，Nginx 和SSLDocker 稍有优势。

顺便测一下应用裸跑的结果，ssl、gzip 为on，RPS为367

Summary:
  Total:    54.3886 secs
  Slowest:  0.4766 secs
  Fastest:  0.0026 secs
  Average:  0.0136 secs
  Requests/sec: 367.7241

Response time histogram:
  0.003 [1] |
  0.050 [19995] |∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎
  0.097 [2] |
  0.145 [0] |
  0.192 [0] |
  0.240 [0] |
  0.287 [1] |
  0.334 [0] |
  0.382 [0] |
  0.429 [0] |
  0.477 [1] |


再测ssl、gzip 为off 情况，具体数据就不贴了，总对比如下：

# 数值为每秒请求数 RPS 
# ssl、gzip on 
Caddy 308 < SSLDocker 317 < Nginx 345 < 裸跑 367
# ssl、gzip off
Nginx 376 < 裸跑 424


拿nginx 反向代理和裸跑对比：

开启ssl、gzip 时性能损失 float(367-345)/367 = 5.99%，
关闭ssl、gzip 时性能损失 float(424-376)/424 = 11.32%。


配置文件

Caddy 配置

mydomain.com {
    gzip
    proxy  / 127.0.0.1:8999
}
mydomain2.com {
    proxy  / 127.0.0.1:8888
}


多个服务

SSLDocker 配置

{
  "Email": "yourmail@domain.com",
  "GzipOn": true,
  "Http2https": true,
  "MaxHeader": 10,
  "Certs": "certs",
  "ProxyItems": [
    {"Host": "mydomain.com", "Target": "http://localhost:8999"},
    {"Host": "mydomain2.com", "Target": "http://localhost:8888"}
  ]
}


也是两个服务

Nginx 配置

主配置nginx.conf 使用默认，使用epoll 模式

server {
    listen 443;
    server_name mydomain.com;

    ssl on;
    ssl_certificate /root/ssl/chained.pem;
    ssl_certificate_key /root/ssl/domain.key;

    ssl_session_timeout 5m;

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA:ECDHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA;
    ssl_prefer_server_ciphers on;

    location ^~ /.well-known/acme-challenge/ {
        alias /var/www/myapp/;
        try_files $uri =404;
    }

    location / {
        proxy_pass_header Server;
        proxy_set_header Host $http_host;
        proxy_redirect off;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Scheme $scheme;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://127.0.0.1:8999;
    }
}


总结

上面的比较是针对个人的使用场景：在一个小VPS 上多挂几个网站而且都使用HTTPS，嫌配置HTTPS 麻烦。以前使用Nginx 则需要注册、配置、验证、添加自动更新任务到Cronjob，如果用Caddy 或SSLDocker，对HTTPS 不用做任何配置，一切都自动完成，其实过程是一样，只是Caddy 和SSLDocker 把这些任务都集成了（go 协程管理真方便）。

Nginx 功能多，成熟稳定；Caddy 功能也在慢慢增多，试着以不同方式实现Nginx 的功能和新鲜的功能；SSLDocker 专注于HTTPS管理和反向代理。如果说Nginx 是成功中年人士，则Caddy 是年轻高富帅，SSLDocker 是嫩屌丝。

相关项目
Nginx (Openresty) https://openresty.org/en/
Caddy https://caddyserver.com/
SSLDocker https://ssldocker.com/
原文出处：golangnote -> https://www.golangnote.com/topic/216.html

```

