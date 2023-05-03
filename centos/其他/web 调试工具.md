```shell
curl 和 curl -I 可以被轻松地应用于 web 调试中，它们的好兄弟 wget 也是如此，或者也可以试试更潮的 httpie。

httpie是一个httpclient工具，能帮助我们快速的进行http请求，类似于curl但是语法做了很多简写，废话不多说，下面看下基本的用法
1.安装httpie
本人使用的是mac，安装时用的homebrew，命令行如下

brew install httpie
等待其安装完成即可，安装完成后简单验证

#命令如下：
http get http://baidu.com
#返回值
HTTP/1.1 200 OK
Accept-Ranges: bytes
Cache-Control: max-age=86400
Connection: Keep-Alive
Content-Length: 81
Content-Type: text/html
Date: Thu, 03 Jan 2019 06:23:27 GMT
ETag: "51-47cf7e6ee8400"
Expires: Fri, 04 Jan 2019 06:23:27 GMT
Last-Modified: Tue, 12 Jan 2010 13:48:00 GMT
Server: Apache

<html>
{
<meta http-equiv="refresh" content="0;url=http://www.baidu.com/">
</html>
代表安装成功，能正常使用
注：github地址是：https://github.com/jakubroztocil/httpie
2.使用比较简单，详情可参考上述github地址
说下方便的点，比如我需要调试我本地的8090端口的/api/device的接口并且我的入参是一个json对象有 userName 和password 两个字段，示例如下：

http :8090/api/device userName=admin password=admin
如上所示即可，返回值会返回详细的http信息
另外如果我是一个大的json文件呢，直接上例子，加入我当前工作目录下有个a.json的入参文件内容如下所示:

{
    "userName": "admin",
    "password": "admin"
}
请求示例如下所示：

http :8090/api/device < a.json
3.带有header信息的请求怎么发送
下面的例子是加一个带有Authentication 的header值


  http post :8080/api/register Authentication:xxxxxxxxx <a.json

```

