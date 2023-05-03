# URL 锚点、fragment 锚部分、锚点定位的九点知识

 2021-12-20 13:50 [小春](tencent://message/?uin=10000&Site=qq.com&Menu=yes)

**摘要：**锚点，又叫命名锚记，是文档中的一种标记，网页设计者可以用它和 URL“锚”在一起，其作用像一个迅速定位器一样，可快速将访问者带到指定位置。

### 一、锚点

锚点，又叫命名锚记，是文档中的一种标记，网页设计者可以用它和 URL“锚”在一起，其作用像一个迅速定位器一样，可快速将访问者带到指定位置。

网页中指定锚点，有两个方法。

- 使用 name 属性，比如 <a name="print"></a>
- 使用 id 属性，比如 <div id="print"></div>

然后在需要的地方，创建到锚点的链接，如：

```
<a href="#print">打印</a>
```

其实 Web 页面上还有一种定位，称为"focus 定位"，也称焦点定位。

当页面上控件，例如文本框、单复选框、按钮等在"可响应聚焦状态"下，通过类似 label for 或 JS ele.focus() 触发焦点选中状态的时候，也会发生定位。

### 二、# 的涵义

[URL](http://www.dedenotes.com/html/url.html) 遵守一种标准的语法，它由协议、域名、端口、路径名称、查询字符串、以及锚部分这六个部分构成，其中端口可以省略，查询字符串和锚部分为参数。具体语法规则如下：

```css
protocol://host:port/pathname?query#fragment
```

在上述语法规则中，protocol 表示协议，host 表示主机名（域名或 IP 地址），port 表示端口，pathname 表示路径名称，query 表示查询字符串，fragment 表示锚部分。

\# 代表网页中的一个位置，作为页面`定位符`出现在 URL 中。其右面的字符，就是该位置的`标识符`（锚部分）。

![image-20220908112928242](../Image/image-20220908112928242.png)

- 左侧的 URL 部分，标识了可由浏览器下载的资源；
- 右侧的部分称为 fragment 标识符（锚部分），指定了资源中的位置。

浏览器读取这个 URL 后，会自动将 print 位置滚动至可视区域。

### 三、HTTP 请求不包括 #

\# 是用来指导浏览器动作的，对服务器端完全无用。所以，HTTP 请求中不包括 #。

比如，访问下面的网址：

```
http://www.example.com/index.html#print
```

浏览器实际发出的请求是这样的：

```
GET /index.html HTTP/1.1
Host: www.example.com
```

可以看到，只是请求 index.html，根本没有 #print 的部分。

### 四、# 后的锚部分

在第一个 # 后面出现的锚部分，都会被浏览器解读为位置标识符。这意味着，锚部分不会被发送到服务端。

比如，下面 URL 的原意是指定一个颜色值：

```
http://www.example.com/?color=#fff
```

但是，浏览器实际发出的请求是：

```
GET index.html/?color= HTTP/1.1
Host: www.example.com
```

可以看到，#fff 被省略了。只有将 # 转码为 %23，浏览器才会将其作为实义字符处理。也就是说，上面的网址应该被写成：

```
http://example.com/?color=%23fff
```

\# 有别于 ?，? 后面的查询字符串会被网络请求发送到服务器，而 fragment 锚部分跟服务器交互没关系，不会被发送到服务器，它主要用于页面中的锚点定位和 HASH 路由切换。

### 五、更改锚部分不会重载网页

单单更改 # 后的锚部分，浏览器只会滚动到相应位置，不会重新加载网页。

比如，从：

```
http://www.example.com/index.html#location1
```

改成：

```
http://www.example.com/index.html#location2
```

浏览器不会重新向服务器请求 index.html。

### 六、更改锚部分会生成历史记录

fragment 锚部分的更改，不会触发浏览器重新加载页面，但是会生成浏览历史。

每一次更改 # 后的锚部分，都会在浏览器的访问历史中增加一个记录，使用"后退"按钮，就可以回到上一个位置。

这个特性对 Ajax 来说特别有用，可以通过设置不同的锚部分，来表示不同的访问状态，并返回不同的内容给用户。

值得注意的是，上述规则对 IE 6 和 IE 7 不成立，它们不会因为锚部分的更改而增加历史记录。

### 七、location.hash

hash 属性是一个可读可写的字符串，该字符串是 URL 的锚部分（从 # 号开始的部分）。

- 读取时，可以用来判断网页状态是否改变，如：

```
window.location.hash
```

就能获取当前 URL 锚部分的 hash 值。

- 写入时，则会在不重载网页的前提下，创造一条访问历史记录。如：

```
location.hash='print'
```

此时浏览器地址栏 URL 锚部分会被更改为 print。

### 八、onhashchange 事件

还可以监听锚部分 hash 值，当 hash 路由切换（hash 值发生变化）时触发 onhashchange 事件。

```
addEventListener("hashchange", function () {
...
});
```

onhashchange 事件是 HTML 5 新增的事件，它的使用方法有三种：

```
window.onhashchange = func;
<body onhashchange="func();">
window.addEventListener("hashchange", func, false);
```

- 如果注册 onhashchange 事件，设置 hash 值会触发事件。可以通过设置 window.onhashchange 注册事件监听器，也可以在 body 元素上设置 onhashchange 属性注册；
- window.location.hash 值的变化，会直接反应到浏览器地址栏（#后面的部分会发生变化）；
- 同时浏览器地址栏 hash 值的变化，也会触发 window.location.hash 值的变化，从而触发 onhashchange 事件。

### 九、Googlebot 会忽略锚部分

默认情况下，Googlebot（Google 爬虫）会忽略 fragment 锚部分。

但是，Google 还规定，如果你希望 Ajax 生成的内容被 Googlebot 爬虫读取，那么 URL 中可以使用（#!），Google 会自动将其后面的内容转成查询字符串 _escaped_fragment_ 的值。

比如，Google 发现示例网站的 URL 如下：

```
http://www.example.com/ajax.html#!dedenotes
```

就会自动抓取另一个 URL：

```
http://www.example.com/ajax.html?_escaped_fragment_=dedenotes
```

通过这种机制，Google 就可以索引动态的 Ajax 内容。