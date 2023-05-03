**url格式组成部分详解**

**URL格式**
在WWW上，每一信息资源都有统一的且在网上唯一的地址，该地址就叫URL（Uniform Resource Locator,统一资源定位器），它是WWW的统一资源定位标志，就是指网络地址。

URL由三部分组成：资源类型、存放资源的主机域名、资源文件名。

也可认为由4部分组成：协议、主机、端口、路径


**协议**

指定使用的传输协议，下表列出 protocol 属性的有效方案名称。 最常用的是HTTP协议，它也是WWW中应用最广的协议。

file 资源是本地计算机上的文件。格式file:///，注意后边应是三个斜杠。

ftp 通过 FTP访问资源。格式 FTP://

gopher 通过 Gopher 协议访问该资源。

http 通过 HTTP 访问该资源。 格式 HTTP://

https 通过安全的 HTTPS 访问该资源。 格式 HTTPS://

mailto 资源为电子邮件地址，通过 SMTP 访问。 格式 mailto:

MMS 通过 支持MMS（流媒体）协议的播放该资源。（代表软件：Windows Media Player）格式 MMS://

ed2k 通过 支持ed2k（专用下载链接）协议的P2P软件访问该资源。（代表软件：电驴） 格式 ed2k://

Flashget 通过 支持Flashget:（专用下载链接）协议的P2P软件访问该资源。（代表软件：快车） 格式 Flashget://

thunder 通过 支持thunder（专用下载链接）协议的P2P软件访问该资源。（代表软件：迅雷） 格式 thunder://

news 通过 NNTP 访问该资源。


**hostname（主机名）**

是指存放资源的服务器的[域名系统](https://baike.baidu.com/item/域名系统)(DNS) 主机名或 IP 地址。有时，在主机名前也可以包含连接到服务器所需的用户名和密码（格式：username:password@hostname）。

**域名**

以www.bilibili.com举例

www代表主机名，主机名与域名组成子域名，子域名可对网站进行业务分类。bilibili.com代表域名，它便于记忆，配合域名与IP地址的绑定和解析，方便大家上网

**port（端口号）**

整数，可选，省略时使用方案的默认端口，各种传输协议都有默认的端口号，如http的默认端口为80。如果输入时省略，则使用默认端口号。有时候出于安全或其他考虑，可以在服务器上对端口进行重定义，即采用非标准端口号，此时，URL中就不能省略端口号这一项。

一些默认端口号如下：

HTTP => 默认端口号80

HTTPS => 默认端口号443

FTP => 默认端口号21


**请求路径（文件名部分）**

“/”之后-?之前 的部分，如果没有“?”,则是从域名后的第一个“/”开始到“#”为止，如果没有“？”和“#”，那么从域名后的第一个“/”开始到结束，都是请求路径。请求路径部分并不是一个URL必须的部分，如果省略该部分，则使用默认的文件名

例如 /video/BV1V7411f7Rf 一般都是请求当前服务对应的项目目录中，video文件夹中的BV1V7411f7Rf 页面。但是也有特殊情况，就是当前的URL是被“伪URL重写”的，我们看到的URL请求其实不是真实的请求。

**query(查询)/传参**

可选，用于给动态网页（如使用CGI、ISAPI、PHP/JSP/ASP/ASP.NET等技术制作的网页）传递参数，可有多个参数，用“&”符号隔开，每个参数的名和值用“=”符号隔开。

**问号传参**

?xxx=xxx&…直到“#”之前

在HTTP事务中，问号传参是客户端把信息传递给服务器的一种方式(也有可能是跳转到某一个页面，把参数值传递给页面用来标识的)

**锚部分（哈希值）**

从“#”开始到最后，都是锚部分。锚部分也不是一个URL必须的部分


**fragment（信息片断）**

字符串，用于指定网络资源中的片断。例如一个网页中有多个名词解释，可使用fragment直接定位到某一名词解释。感觉挺少见的。

**例子**

```css
http://www,zhufengpeixun.cn:80/stu/index.html?name=xxx&age=25#teacher
```

传输协议：http:，域名：www,zhufengpeixun.cn，端口号：80，请求路径：/stu/index.html，问号传参：?name=xxx&age=25，锚部分：#teacher

```css
http://www.aspxfans.com:8080/news/index.asp?boardID=5&ID=24618&page=1#name
```

协议：http:，域名：www.aspxfans.com，端口号：8080，请求路径：/news/index.asp，问号传参：?boardID=5&ID=24618&page=1，锚部分：#name

