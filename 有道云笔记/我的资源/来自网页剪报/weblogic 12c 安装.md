**一、WebLogic的介绍**

  WebLogic是美国bea公司出品的一个application server，确切的说是一个基于Javaee架构的中间件，纯java开发的，最新版本WebLogic Server 9.0是迄今为止发布的最卓越的BEA应用服务器。BEA WebLogic是用于开发、集成、部署和管理大型分布式Web应用、网络应用和数据库应用的Java应用服务器。将Java的动态功能和Java Enterprise标准的安全性引入大型网络应用的开发、集成、部署和管理之中。完全遵循J2EE 1.4规范。

WebLogic与Tomcat的区别：

  WebLogic更加强大。weblogic是j2ee的应用服务器（application server），包括ejb ,jsp,servlet,jms等等，全能型的。是商业软件里排名第一的容器（JSP、servlet、EJB等），并提供其他如JAVA编辑等工具，是一个综合的开发及运行环境。

  WebLogic应该是J2EE Container(Web Container + EJB Container + XXX规范)，而Tomcat只能算Web Container，是官方指定的JSP&Servlet容器。只实现了JSP/Servlet的相关规范，不支持EJB。不过Tomcat配合jboss和apache可以实现j2ee应用服务器功能

**二、WebLogic下载**

来到Oracle的WebLogic Server主页：

http://www.oracle.com/technetwork/middleware/weblogic/overview/index.html

点击Downloads：

进入Downloads界面之后，点击Download file进行下载：

注意：

(1)别忘记点击上面的“同意”条款。

(2)点击下拉框选择下载的版本，分别为简版/普通和完全版。

我们这里选择下载的是Generic版本。大家可以根据自己的需要选择下载。

如果Oracle抽了，大家去这里下载12.2.3的版本：

https://pan.baidu.com/share/link?shareid=2935789172&uk=1493614172&fid=1093830637966671

我们下载完毕之后，会有一个压缩包：

![image-20221021153105132](../../../Image/image-20221021153105132.png)

三、WebLogic安装

将我们下载的压缩包解压：

![image-20221021153121105](../../../Image/image-20221021153121105.png)

打开目录中的Disk/install安装目录，找到名为ng.cmd的脚本文件：

![image-20221021153132202](../../../Image/image-20221021153132202.png)

双击打开脚本文件，弹出一个黑色的控制台：

稍等片刻，就会出现WebLogic的安装界面：

![image-20221021153201766](../../../Image/image-20221021153201766.png)

我们接下来按照以下步骤进行安装：

![image-20221021153214680](../../../Image/image-20221021153214680.png)

点击“下一步”

![image-20221021153226567](../../../Image/image-20221021153226567.png)

点击“下一步”

![image-20221021153238930](../../../Image/image-20221021153238930.png)

点击“下一步”

![image-20221021153250565](../../../Image/image-20221021153250565.png)

点击“下一步”

![image-20221021153303008](../../../Image/image-20221021153303008.png)

点击“下一步”

![image-20221021153314902](../../../Image/image-20221021153314902.png)

点击“下一步”

![image-20221021153326035](../../../Image/image-20221021153326035.png)

点击“安装”

![image-20221021153336049](../../../Image/image-20221021153336049.png)

点击“下一步”

![image-20221021153346913](../../../Image/image-20221021153346913.png)

点击完成，WebLogic就安装完毕了。接下来会弹出WebLogic的配置界面。

三、WebLogic的配置

安装完毕之后，会弹出配置窗口，我们按照以下操作进行配置：

![image-20221021154422985](../../../Image/image-20221021154422985.png)

点击“下一步”

![image-20221021154432136](../../../Image/image-20221021154432136.png)

点击“下一步”

![image-20221021154441526](../../../Image/image-20221021154441526.png)

点击“下一步”

![image-20221021154453823](../../../Image/image-20221021154453823.png)

点击“下一步”

![image-20221021154503528](../../../Image/image-20221021154503528.png)

点击“下一步”

![image-20221021154512322](../../../Image/image-20221021154512322.png)

点击“下一步”

![image-20221021154522999](../../../Image/image-20221021154522999.png)

点击“下一步”

![image-20221021154534369](../../../Image/image-20221021154534369.png)

点击“下一步”

![image-20221021154543423](../../../Image/image-20221021154543423.png)

点击“下一步”

![image-20221021154557167](../../../Image/image-20221021154557167.png)

点击“下一步”

![image-20221021154751998](../../../Image/image-20221021154751998.png)

点击“下一步”

![image-20221021154804472](../../../Image/image-20221021154804472.png)

点击“下一步”

![image-20221021154815870](../../../Image/image-20221021154815870.png)

点击“下一步”

![image-20221021154827137](../../../Image/image-20221021154827137.png)

点击“下一步”

![image-20221021154837061](../../../Image/image-20221021154837061.png)

点击“创建”

![image-20221021154847573](../../../Image/image-20221021154847573.png)

![image-20221021154854235](../../../Image/image-20221021154854235.png)

点击“下一步”

![image-20221021154904654](../../../Image/image-20221021154904654.png)

看到完成界面，就说明我们的WebLogic的安装与配置都已经完成了。

四、简单操作WebLogic

找到我们刚才安装的WebLogic的所在目录：

![image-20221021154918841](../../../Image/image-20221021154918841.png)

打开\user_projects\domains\base_domain目录：

![image-20221021155027863](../../../Image/image-20221021155027863.png)

点击该目录下的startWebLogic.cmd脚本，运行WebLogic。

![image-20221021155038636](../../../Image/image-20221021155038636.png)

我们在网页浏览器中输入"http://localhost:7001/console"地址，

就可以访问WebLogic的控制台了：

![image-20221021155058236](../../../Image/image-20221021155058236.png)

输入我们之前填写的管理员的账号密码，登录到WebLogic管理系统中：

![image-20221021155112299](../../../Image/image-20221021155112299.png)

至此，我们的WebLogic的安装与配置就讲解完毕。请关注后面的WebLogic的后续文章。

**转载请注明出处：**http://blog.csdn.net/acmman/article/details/70093877

