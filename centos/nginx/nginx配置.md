```shell
nginx proxy_set_header设置，自定义header
在实际应用中，我们可能需要获取用户的ip地址，比如做异地登陆的判断，或者统计ip访问次数等，通常情况下我们使用request.getRemoteAddr()就可以获取到客户端ip，但是当我们使用了nginx作为反向代理后，使用request.getRemoteAddr()获取到的就一直是nginx服务器的ip的地址，那这时应该怎么办？
   

而且有些场景做了一些客户端浏览器url的判断，比如，浏览器输入baidu.com是可以访问到百度的，但是输入！@#￥*.com有可能也是可以访问到百度，但是百度内部并不希望以这种方式访问（或者防止一些网络攻击），这时候应该怎么办？
   

其实nginx允许重新定义或者添加发往后端服务器的请求头。value可以包含文本、变量或者它们的组合。

   

默认情况下，有两个请求头会被重新定义：

proxy_set_header Host $proxy_host; //默认会将后端服务器的HOST填写进去

proxy_set_header Connection close;

   

   

我们可以通过设置nginx配置去调整转发报文的头部：

   

proxy_set_header X-real-ip $remote_addr;
其中这个X-real-ip是一个自定义的变量名，名字可以随意取，这样做完之后，用户的真实ip就被放在X-real-ip这个变量里了，然后，在web端可以这样获取：

   

request.getHeader("X-real-ip")

   

proxy_set_header X-Forwarded-For $remote_addr;
同上。

真实的显示出客户端原始ip。（nginx更多使用这条配置，X-Forwarded-For为默认字段，以下介绍均为默认字段）

   

proxy_set_header Host $http_host;
如果想获取客户端访问的头部，可以这样来设置。

但是，如果客户端请求头中没有携带这个头部，那么传递到后端服务器的请求也不含这个头部。

   

proxy_set_header Host $host;
这个配置相当于上面配置的增强。

它的值在请求包含"Host"请求头时为"Host"字段的值，在请求未携带"Host"请求头时为虚拟主机的主域名。

   

proxy_set_header Host $host:$proxy_port;
服务器名和后端服务器的端口（访问端口）一起传送。

   

proxy_set_header <<<*>>> "";
请求头的值为空，请求头将不会传送给后端服务器。

   

proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
在默认情况下经过proxy转发的请求，在后端看来远程地址都是proxy端的ip 。

添加这条配置之后：

意思是增加一个$proxy_add_x_forwarded_for到X-Forwarded-For里去，注意是增加，而不是覆盖，当然由于默认的X-Forwarded-For值是空的，所以我们总感觉X-Forwarded-For的值就等于$proxy_add_x_forwarded_for的值，实际上当你搭建两台nginx在不同的ip上，并且都使用了这段配置，那你会发现在web服务器端通过request.getHeader("X-Forwarded-For")获得的将会是客户端ip和第一台nginx的ip。

   

在第一台nginx中,使用

   

proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

   

现在的$proxy_add_x_forwarded_for变量的"X-Forwarded-For"部分是空的，所以只有$remote_addr，而$remote_addr的值是用户的ip，于是赋值以后，X-Forwarded-For变量的值就是用户的真实的ip地址了。

   

到了第二台nginx，也使用

   

proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

   

现在的$proxy_add_x_forwarded_for变量，

X-Forwarded-For部分包含的是用户的真实ip，

$remote_addr部分的值是上一台nginx的ip地址，

于是通过这个赋值以后现在的X-Forwarded-For的值就变成了"用户的真实ip，第一台nginx的ip"。


---------------------------------------------------------------------
upstream按照轮询（默认）方式进行负载，每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。虽然这种方式简便、成本低廉。但缺点是：可靠性低和负载分配不均衡。适用于图片服务器集群和纯静态页面服务器集群。

    除此之外，upstream还有其它的分配策略，分别如下：

    weight（权重）

    指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均的情况。如下所示，10.0.0.88的访问比率要比10.0.0.77的访问比率高一倍。

upstream linuxidc{ 
      server 10.0.0.77 weight=5; 
      server 10.0.0.88 weight=10; 
}

    ip_hash（访问ip）

    每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题。

upstream favresin{ 
      ip_hash; 
      server 10.0.0.10:8080; 
      server 10.0.0.11:8080; 
}

    fair（第三方）

    按后端服务器的响应时间来分配请求，响应时间短的优先分配。与weight分配策略类似。

 upstream favresin{      
      server 10.0.0.10:8080; 
      server 10.0.0.11:8080; 
      fair; 
}

url_hash（第三方）

按访问url的hash结果来分配请求，使每个url定向到同一个后端服务器，后端服务器为缓存时比较有效。

注意：在upstream中加入hash语句，server语句中不能写入weight等其他的参数，hash_method是使用的hash算法。

 upstream resinserver{ 
      server 10.0.0.10:7777; 
      server 10.0.0.11:8888; 
      hash $request_uri; 
      hash_method crc32; 
}

upstream还可以为每个设备设置状态值，这些状态值的含义分别如下：

down 表示单前的server暂时不参与负载.

weight 默认为1.weight越大，负载的权重就越大。

max_fails ：允许请求失败的次数默认为1.当超过最大次数时，返回proxy_next_upstream 模块定义的错误.

fail_timeout : max_fails次失败后，暂停的时间。

backup： 其它所有的非backup机器down或者忙的时候，请求backup机器。所以这台机器压力会最轻。

upstream bakend{ #定义负载均衡设备的Ip及设备状态 
      ip_hash; 
      server 10.0.0.11:9090 down; 
      server 10.0.0.11:8080 weight=2; 
      server 10.0.0.11:6060; 
      server 10.0.0.11:7070 backup; 
}
```

