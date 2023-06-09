```
Nginx配置中的if判断

当rewrite的重写规则满足不了需求时，比如需要判断当文件不存在时、当路径包含xx时等条件，则需要用到if

if语法
if (表达式) {
    ...
}
表达式语法：

当表达式只是一个变量时，如果值为空或任何以0开头的字符串都会当做false
直接比较变量和内容时，使用=或!=
-f和!-f用来判断是否存在文件
-d和!-d用来判断是否存在目录
-e和!-e用来判断是否存在文件或目录
-x和!-x用来判断文件是否可执行
为了配置if的条件判断，这里需要用到nginx中内置的全局变量

$args               这个变量等于请求行中的参数，同$query_string
$content_length     请求头中的Content-length字段。
$content_type       请求头中的Content-Type字段。
$document_root      当前请求在root指令中指定的值。
$host               请求主机头字段，否则为服务器名称。
$http_user_agent    客户端agent信息
$http_cookie        客户端cookie信息
$limit_rate         这个变量可以限制连接速率。
$request_method     客户端请求的动作，通常为GET或POST。
$remote_addr        客户端的IP地址。
$remote_port        客户端的端口。
$remote_user        已经经过Auth Basic Module验证的用户名。
$request_filename   当前请求的文件路径，由root或alias指令与URI请求生成。
$scheme             HTTP方法（如http，https）。
$server_protocol    请求使用的协议，通常是HTTP/1.0或HTTP/1.1。
$server_addr        服务器地址，在完成一次系统调用后可以确定这个值。
$server_name        服务器名称。
$server_port        请求到达服务器的端口号。
$request_uri        包含请求参数的原始URI，不包含主机名，如：”/foo/bar.php?arg=baz”。
$uri                不带请求参数的当前URI，$uri不包含主机名，如”/foo/bar.html”。
$document_uri       与$uri相同。
举例说明
1、如果文件不存在则返回400

if (!-f $request_filename) {
    return 400;
}
2、如果host不是jouypub.com，则301到jouypub.com中

if ( $host != 'jouypub.com' ){
    rewrite ^/(.*)$ https://jouypub.com/$1 permanent;
}
3、如果请求类型不是POST则返回405

if ($request_method = POST) {
    return 405;
}
4、如果参数中有a=1则301到指定域名

if ($args ~ a=1) {
    rewrite ^ http://example.com/ permanent;
}
5、在某种场景下可结合location规则来使用，如：

# 访问 /test.html 时
location = /test.html {
    # 设置默认值为xiaowu
    set $name xiaowu;
    # 如果参数中有 name=xx 则使用该值
    if ($args ~* name=(\w+?)(&|$)) {
        set $name $1;
    }
    # 301
    rewrite ^ /$name.html permanent;
}
上面表示：
/test.html => /xiaowu.html
/test.html?name=ok => /ok.html?name=ok
```

