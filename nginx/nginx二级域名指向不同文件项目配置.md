```
nginx二级域名指向不同文件项目配置

需要使用泛域名解析，并且加上空的判断，以保证没有二级域名的也可以访问

核心配置

server_name ~^(?<subdomain>.+)\.caipudq\.cn$;
if ( $subdomain = '') {
    set $subdomain "tp5caipu";
}

if ( $subdomain = 'www') {
    set $subdomain "tp5caipu";
}
root /mnt/www/$subdomain/public;

======================================================
if ($subdomain = ''){           #判断二级域名是非为空
    set $subdomain www;     #将空的二级域名设置为默认的“www”
}                               #如果不设置，Nginx将会返回404
server_name ~^(?<subdomain>.+).cpms.tech$;

======================================================

A named regular expression capture can be used later as a variable:
命名正则表达式捕获稍后可以用作变量：

server {
    server_name   ~^(www\.)?(?<domain>.+)$;

    location / {
        root   /sites/$domain;
    }
}
The PCRE library supports named captures using the following syntax:
PCRE 库使用以下语法支持命名捕获：

?<name>	Perl 5.10 兼容语法，自 PCRE-7.0 起支持
?'name'	Perl 5.10 兼容语法，自 PCRE-7.0 起支持
?P<name>	Python 兼容语法，自 PCRE-4.0 起支持
```

