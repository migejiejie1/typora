```shell
jps
相关命令：暂无相关命令
jps 可以列出本机所有Java进程的pid 

jps [ options ] [ hostid ] 

选项 
-q 仅输出VM标识符，不包括class name,jar name,arguments in main method 
-m 输出main method的参数 
-l 输出完全的包名，应用主类名，jar的完全路径名 
-v 输出jvm参数 
-V 输出通过flag文件传递到JVM中的参数(.hotspotrc文件或-XX:Flags=所指定的文件 
-Joption 传递参数到vm,例如:-J-Xms48m

===========================================================

[root@manage ~]# jps 
31687 Jps
4545 Application
[root@manage ~]# jps -q
31711
4545
[root@manage ~]# jps -m
31732 Jps -m
4545 Application
[root@manage ~]# jps -l
31752 sun.tools.jps.Jps
4545 com.aliyun.tianji.cloudmonitor.Application
[root@manage ~]# jps -v
31776 Jps -Dapplication.home=/usr/local/jdk1.7.0_80 -Xms8m
4545 Application -Djava.compiler=none -XX:-UseGCOverheadLimit -XX:NewRatio=1 -XX:SurvivorRatio=8 -XX:+UseSerialGC -Djava.io.tmpdir=../../tmp -Xms16m -Xmx32m -Djava.library.path=../lib:../../lib -Dwrapper.key=mo2qx3bGSbauRl9s -Dwrapper.port=32000 -Dwrapper.jvm.port.min=31000 -Dwrapper.jvm.port.max=31999 -Dwrapper.disable_console_input=TRUE -Dwrapper.pid=4543 -Dwrapper.version=3.5.27 -Dwrapper.native_library=wrapper -Dwrapper.arch=x86 -Dwrapper.service=TRUE -Dwrapper.cpu.timeout=10 -Dwrapper.jvmid=1
[root@manage ~]# jps -V
31797 Jps
4545 Application

```

