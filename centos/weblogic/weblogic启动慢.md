```shell
Weblogic在Linux下启动特别慢及进入控制台慢的解决方法
实际是JVM在Linux下的bug
　　他想调用一个随机函数

　　但取不到

　　暂时的解决办法是

　　1）较好的解决办法： 在Weblogic启动参数里添加 “-

　　Djava.security.egd=file:/dev/./urandom” (/dev/urandom 无法启动)

　　2）最差的解决办法： 执行命令 mv /dev/random /dev/random.ORIG ; ln /dev/urandom /dev/random

　　将/dev/random 指向/dev/urandom

　　3)最好的解决办法:  修改Linux上Weblogic使用的jdk $JAVA_HOME/jre/lib/security/java.security 文件

　　将securerandom.source=file:/dev/urandom 修改为

　　securerandom.source=file:/dev/./urandom

　　这样可以解决任何一个域Weblogic启动慢的问题
```

