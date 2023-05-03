```shell
Java中的JAR/EAR/WAR包的文件夹结构说明（转）
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

复制代码
tools.jar 
  |  resource.xml                    // 资源配置文件 
  |  other.xml 
  | 
  |— META-INF           
               MANIFEST.MF          // jar包的描述文件 
  |— com                            // 类的包目录 
         |—test  
                   util.class       // java类文件 
复制代码
2、WAR包 :
WAR(Web Archive file)网络应用程序文件，是与平台无关的文件格式，它允许将许多文件组合成一个压缩文件。war专用在web方面 。

JAVA WEB工程，都是打成WAR包进行发布。

典型的war包内部结构如下：

复制代码
webapp.war 
  |    index.jsp 
  | 
  |— images 
  |— META-INF 
  |— WEB-INF 
          |   web.xml                   // WAR包的描述文件 
          | 
          |— classes 
          |          action.class       // java类文件，编译后的字节码
          | 
          |— lib 
                    other.jar           // 依赖的jar包 
                    share.jar 
复制代码
3、EAR包：
JAR（Java Archive，Java 归档文件）是与平台无关的文件格式，它允许将许多文件组合成一个压缩文件。

为 J2EE 应用程序创建的 JAR 文件是 EAR 文件（企业 JAR 文件）。

针对企业级项目，实际上EAR包中包含WAR包和几个企业级项目的配置文件而已，一般服务器选择WebSphere等，都会使用EAR包。

典型的ear包内部结构如下：

复制代码
app.ear 
   |   ejb.jar                // ejb-jar包 
   |   other.jar              // 普通的jar包 
   |   webapp.war         　　// war包 
   | 
   |—META-INF 
          application.xml 　 // EAR描述文件 
复制代码
WEB标准包是war方式，J2EE标准包使用的是ear方式，区别就在与你必须在支持j2ee的环境下才能使用ear方式，比如在tomcat中就不能使用ear方式，但是在weblogic中两种都可以，ear方式所包含的范围比war方式广很多，就好比一个大圆里面的小圆，是包含与被包含关系 。
```

```shell
jar包和war包都可以看成压缩文件，都可以用解压软件打开，jar包和war包都是为了项目的部署和发布，通常在打包部署的时候，会在里面加上部署的相关信息。这个打包实际上就是把代码和依赖的东西压缩在一起，变成后缀名为.jar和.war的文件，就是我们说的jar包和war包。但是这个“压缩包”可以被编译器直接使用，把war包放在tomcat目录的webapp下，tomcat服务器在启动的时候可以直接使用这个war包。通常tomcat的做法是解压，编译里面的代码，所以当文件很多的时候，tomcat的启动会很慢。
jar包和war包的区别：jar包是java打的包，war包可以理解为javaweb打的包，这样会比较好记。jar包中只是用java来写的项目打包来的，里面只有编译后的class和一些部署文件。而war包里面的东西就全了，包括写的代码编译成的class文件，依赖的包，配置文件，所有的网站页面，包括html，jsp等等。一个war包可以理解为是一个web项目，里面是项目的所有东西。
什么时候使用jar包或war包？当你的项目在没有完全完成的时候，不适合使用war文件，因为你的类会由于调试之类的经常改，这样来回删除、创建war文件很不方便，来回修改，来回打包，最好是你的项目已经完成了，不做修改的时候，那就打个war包吧，这个时候一个war文件就相当于一个web应用程序；而jar文件就是把类和一些相关的资源封装到一个包中，便于程序中引用

jar包和war包的区别：


jar包就是别人已经写好的一些类，然后将这些类进行打包，你可以将这些jar包引入你的项目中，然后就可以直接使用这些jar包中的类和属性了，这些jar包一般都会放在lib目录下。

war是一个web模块，其中需要包括WEB-INF，是可以直接运行的WEB模块。而jar一般只是包括一些class文件，在声明了Main_class之后是可以用java命令运行的。
它们都是压缩的包,拿Tomcat来说,将war文件包放置它的\webapps\目录下，启动Tomcat,这个包可以自动进行解压，也就是你的web目录，相当于发布了。  
war包:是做好一个web应用后，通常是网站，打成包部署到容器中。
jar包：通常是开发时要引用通用类，打成包便于存放管理。
ear包：企业级应用，通常是EJB打成ear包。
所有的包都是用jar打的，只不过目标文件的扩展名不一样。
WAR是Sun提出的一种Web应用程序格式，与JAR类似，也是许多文件的一个压缩包。这个包中的文件按一定目录结构来组织：通常其根目录下包含有Html和Jsp文件或者包含这两种文件的目录，另外还会有一个WEB-INF目录，这个目录很重要。通常在WEB-INF目录下有一个web.xml文件和一个classes目录，web.xml是这个应用的配置文件，而classes目录下则包含编译好的Servlet类和Jsp或Servlet所依赖的其它类（如JavaBean）。通常这些所依赖的类也可以打包成JAR放到WEB-INF下的lib目录下，当然也可以放到系统的CLASSPATH中，但那样移植和管理起来不方便。
```

