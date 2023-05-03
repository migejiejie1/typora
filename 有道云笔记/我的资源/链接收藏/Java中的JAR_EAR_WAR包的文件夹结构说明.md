```shell
JAR包：打成JAR包的代码，一般作为工具类，在项目中，会应用到N多JAR工具包。

WAR包：JAVA WEB工程，都是打成WAR包，进行发布，如果我们的服务器选择TOMCAT等轻量级服务器，一般就打出WAR包进行发布。

EAR包：这针对企业级项目的，实际上EAR包中包含WAR包和几个企业级项目的配置文件而已，一般服务器选择WebSphere等，都会使用EAR包。

 

文件夹及作用说明：

1、JAR包 ：

JAR 文件格式以流行的 ZIP 文件格式为基础。

与 ZIP 文件不同的是，JAR 文件不仅用于压缩和发布，而且还用于部署和封装库、组件和插件程序，并可被像编译器和 JVM 这样的工具直接使用。

JAR 文件与 ZIP 文件唯一的区别就是在 JAR 文件的内容中，包含了一个 META-INF/MANIFEST.MF 文件，这个文件是在生成 JAR 文件的时候自动创建的。

作用：

作为工具包和类库；这个是最基本的作用，在大型项目中，一般会依赖N多JAR包。作为应用工程和扩展的构建单元；开发大型应用的时候，一般会将应用分成几个单元，每个单元用jar包封装，并相互依赖。作为组件、applet 或者插件程序的部署单位；用于打包与组件相关联的辅助资源。

典型的jar包内部结构如下：

tools.jar   |  resource.xml                    // 资源配置文件   |  other.xml   |   |— META-INF                          MANIFEST.MF          // jar包的描述文件   |— com                            // 类的包目录          |—test                     util.class       // java类文件 

2、WAR包 :

WAR(Web Archive file)网络应用程序文件，是与平台无关的文件格式，它允许将许多文件组合成一个压缩文件。war专用在web方面 。

JAVA WEB工程，都是打成WAR包进行发布。

典型的war包内部结构如下：

webapp.war   |    index.jsp   |   |— images   |— META-INF   |— WEB-INF           |   web.xml                   // WAR包的描述文件           |           |— classes           |          action.class       // java类文件，编译后的字节码          |           |— lib                     other.jar           // 依赖的jar包                     share.jar 

3、EAR包：

JAR（Java Archive，Java 归档文件）是与平台无关的文件格式，它允许将许多文件组合成一个压缩文件。

为 J2EE 应用程序创建的 JAR 文件是 EAR 文件（企业 JAR 文件）。

针对企业级项目，实际上EAR包中包含WAR包和几个企业级项目的配置文件而已，一般服务器选择WebSphere等，都会使用EAR包。

典型的ear包内部结构如下：

app.ear    |   ejb.jar                // ejb-jar包    |   other.jar              // 普通的jar包    |   webapp.war         　　// war包    |    |—META-INF           application.xml 　 // EAR描述文件 

WEB标准包是war方式，J2EE标准包使用的是ear方式，区别就在与你必须在支持j2ee的环境下才能使用ear方式，比如在tomcat中就不能使用ear方式，但是在weblogic中两种都可以，ear方式所包含的范围比war方式广很多，就好比一个大圆里面的小圆，是包含与被包含关系 。

 

原文：http://hck.iteye.com/blog/1751951
```

