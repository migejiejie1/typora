# VNC 选型参考

近期在做VNC相关的需求，但是对此完全没有任何了解。而中文互联网博客上，关于VNC的相关文章很多都关注于个别具体的案例，时而冒出来tigerVNC，时而又是tightVNC。这样在看不到任何VNC全貌的情况下，会让人困惑不已。

所以我就只有去英文互联网上查找相关资料，索性，英文互联网上有不少关于VNC的更全面的介绍，我看了之后，也就结合我的理解翻译过来一部分，供后续有相关需求的人查看。

### 这篇博客主要回答的问题是：VNC是干嘛的？VNC的基本组成？VNC有怎样的产品生态？各类产品优劣是什么？

- VNC是干嘛的？
  VNC全称Virtual Network Computing ，顾名思义——虚拟网络计算。VNC的使用场景和SSH远程登录服务器操作的场景很接近。区别在于VNC具有可视化的桌面而SSH只能通过命令行来操作。所以，我们可以明确，VNC就是一个通过网络对于一个计算机进行远程操控的具备桌面化的工具。
- VNC的组成？
  VNC有两个组成部分，vncserver 和vncviewer。一台机器安装了vncserver后，才可以通过vncviewer访问。至于vncviewer的具体其他情况，我还没有实装，说不太好，这部分需要整个项目跑起来后，回来补充。
- VNC的产品生态和选型既个产品的优劣对比

> TightVNC
> TightVNC的Server和Viewer 针对于低速网络连接设计使用了特殊的数据编码技术，第一个公开版本发行于2001年，最新的TightVNC 可以在所有的现在流行的桌面系统中运行，并且开发了一个JAVA版本的Viewer。

##### 优点：

1，免费下载和使用
2，易于上手和学习
3，在远程机器上配置简易
4，体积小，耗费极少系统资源

##### 缺点：

1,屏幕刷新可能存在延迟
2,相对于其他VNC，消耗更多网络资源
3,应用表现有些落后于时代
4,缺乏其他一些VNC应用提供的一些高级特性

> TigerVNC TigerVNC最初由Red Hat公司开发出来，旨在于提升TightVNC。TigerVNC以TightVNC的一份代码快照为基础来进行开发，如今，已经扩展为支持linux，mac，windows系统，并且具备许多应用和安全方面的增强。

##### 优点：

1，支持全部主流操作系统：linux，MacOS，Windows
2，对于加密和安全认证特性具可扩展性
3，拥有庞大的用户社群

##### 缺点：

1，对于新手用户，一些高级特性的使用需要相当程度的学习曲线。

> RealVNC：VNC Connect VNC Connect 由RealVNC公司开发销售的VNC应用。有商用的专业版和企业版，也有非商用的家庭版。

##### 优点：

1，家庭版免费
2，轻量且快速
3，任何VNC客户端都可以
4，适用于各个平台

##### 缺点:

1，可能在公司网络环境下会遇到一些表现问题(May experience performance issues inside corporate networks.)
2，相对于其他VNC应用，RealVNC的产品的配置更加复杂。
3，多用于企业用户

> Chicken(of the VNC) Chicken是一款在MAC OS 上运行的开源的VNC client 软件，且不具备VNC server的功能。Chicken可以匹配多种VNC server 包括UltraVNC。

##### 优点

1，轻量且快速
2，自动发现VNC server
3，具有很强的加密特性
4，远程桌面的刷新极为准确

##### 缺点

1，只能在Mac OS上用
2，只有VNC的客户端

> JollysFastVNC 由开发者 Patrick Stein 开发的一款VNC Client 软件，在MacOS上使用，集成了SSH通道。

##### 优点

1，真的很快
2，支持多种加密协议
3，支持Retina屏显示
4，灵活的桌面尺寸缩放

##### 缺点

1，不免费
2，只能在MAC OS上使用
3，不支持多显示器
4，配置过程过于复杂

> Mocha VNC Lite MochaSoft 针对于IPhone和IPad提供了完全商用的VNC产品和免费的Lite版 VNC产品。相对于商用版本，Lite版本缺少对于 一些键盘按键（Ctrl,Alt,Del）和一些鼠标操作(右键，点击拖拽)等的操作。这家公司正在测试这个客户端对于Real VNC,Tight VNC 和UltraVNC的支持。