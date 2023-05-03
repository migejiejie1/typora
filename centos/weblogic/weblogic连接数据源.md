```shell
jdbc:oracle:thin:@192.168.3.98:1521:orcl（详解）

整理自互联网

一、

jdbc:oracle:thin:@192.168.3.98:1521:orcl
jdbc:表示采用jdbc方式连接数据库
oracle:表示连接的是oracle数据库
thin:表示连接时采用thin模式(oracle中有两种模式)

jdbc:oralce:thin:是一个jni方式的命名

@表示地址
1521和orcl表示端口和数据库名

@192.168.3.98:1521:orcl整个是一块
也就是说是这样[jdbc]:[oracle]:[thin]:[@192.168.3.98:1521:orcl]

二、

oracle的jdbc连接方式:oci和thin
 
    oci和thin是Oracle提供的两套Java访问Oracle数据库方式。

    thin是一种瘦客户端的连接方式，即采用这种连接方式不需要安装oracle客户端,只要求classpath中包含jdbc驱动的jar包就行。thin就是纯粹用Java写的ORACLE数据库访问接口。
oci是一种胖客户端的连接方式，即采用这种连接方式需要安装oracle客户端。oci是Oracle Call Interface的首字母缩写，是ORACLE公司提供了访问接口，就是使用Java来调用本机的Oracle客户端，然后再访问数据库，优点是速度 快，但是需要安装和配置数据库。

     从相关资料可以总结出以下几点：
1. 从使用上来说，oci必须在客户机上安装oracle客户端或才能连接，而thin就不需要，因此从使用上来讲thin还是更加方便，这也是thin比较常见的原因。 
2. 原理上来看，thin是纯java实现tcp/ip的c/s通讯；而oci方式,客户端通过native java method调用c library访问服务端，而这个c library就是oci(oracle called interface)，因此这个oci总是需要随着oracle客户端安装（从oracle10.1.0开始，单独提供OCI Instant Client，不用再完整的安装client） 
3. 它们分别是不同的驱动类别，oci是二类驱动， thin是四类驱动，但它们在功能上并无差异。

    从使用thin驱动切换到oci驱动在配置来说很简单，只需把连接字符串java:oracle:thin:@hostip:1521:实例名换为java:oracle:oci@本地服务名即可。如:从　　
jdbc:oracle:thin:@10.1.1.2:1521:shdb　　
改成　　
jdbc:oracle:oci8:@shdb　　
但 这里这台机需安装oracle数据库的客户端并配置本地服务名，同时还需指定NLS_LANG环境变量，NLS_LANG环境变量是用来控制客户端在显示 oracle数据库的数据时所用的字符集和本地化习惯。通常把NLS_LANG的字符集部分指定为数据库所用的字符集则就不会存在java显示的乱码问题 了。　　
对于oracle数据库客户端的安装，有二种选择，一是老实的用oracle数据库的安装光盘安装对应版本的oracle客户端。二是下载oracle提从的即时客户端，即时客户端是不用安装的，把下载包解压即可。　　
要使java web正常的通过oci驱动访问oracle，还需要客户端正确的配置一下相关变量。主要如下:　　
对于windows系统并使用oracle客户端时:　　
1. 把%ORACLE_HOME%lib加到PATH环境变量.　　
2. 把%ORACLE_HOME%jdbclibclasses12.jar加到CLASSPATH环境变量里.也可以把classes12.jar拷贝到tomcat的commanlib目录下。　　
对于windows系统并使用oracle的即时客户端时(假定即时客户端解压在d盘):　　
1. 把d:instantclient_10_2加到PATH环境变量　　
2. 把d:instantclient_10_2classes12.jar加到CLASSPATH环境变量里.也可以把classes12.jar拷贝到tomcat的commanlib目录下。　　


对于Linux系统并使用oracle客户端时:　　
1. 在使用tomcat的用户主目录下的.bash_profile文件中加入　　
exprot ORACLE_HOME=/u01/app/oracle/prodUCt/9.2.0.4　　
export LD_LIBRARY_PATH=$ORACLE_HOME/lib　　
2. 把classes12.jar拷贝到tomcat的commanlib目录下。

　　
对于linux系统并使用oracle即时客户端时:　　
1. 在使用tomcat的用户主目录下的.bash_profile文件中加入　　
exprot ORACLE_HOME=/instantclient_10_2　　
export LD_LIBRARY_PATH=$ORACLE_HOME/lib　　
2. 把instantclient_10_2目录下的classes12.jar拷贝到tomcat的commanlib目录下。

　　
假如一个tomcat下带了几个应用，且几个应用都要连接oracle数据库时，则要注重的时，不要在每个应用的WEB- INF/lib目录下放入oracle的classes12.jar/zip文件。而应该把classes12.jar/zip文件放到tomcat的 common/lib目录下。否则会出来ojdbclib9/10库重复加载的错误。　　

    使用oracle即时客户端是，本地服务名的建立可以在目录instantclient_10_2下建立tnsnames.ora下添加连接串，如:　　

SHDB =(DESCRIPTION =(ADDRESS_LIST =(ADDRESS = (PROTOCOL = TCP)(HOST = 10.1.1.236)(PORT = 1521)))　　(CONNECT_DATA =(SERVICE_NAME = shdb)))即可。

来自: http://hi.baidu.com/anboqing/blog/item/5a7b49f4e36fb57ddcc4744a.html
```

