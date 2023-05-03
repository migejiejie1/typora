```shell
##  JVM configuration
##  警告:不要编辑此文件。 如果要重写JVM选项，或设置任何其他选项，您应该在jvm.options.d中创建一个或多个文件包含调整的目录。  

## 堆大小, 最小值和最大值应该设置为相同的值 https://www.elastic.co/guide/en/elasticsearch/reference/7.16/heap-size.html
-Xms2g
-Xmx2g

## 专家设置
##以下所有设置都被认为是专家设置。除非你明白自己在做什么，否则不要调整它们。不要在这个文件中编辑它们;方法创建一个新文件在jvm.options.d目录，包含你的调整。

## GC configuration
8-13:-XX:+UseConcMarkSweepGC
8-13:-XX:CMSInitiatingOccupancyFraction=75
8-13:-XX:+UseCMSInitiatingOccupancyOnly

## G1GC Configuration
# NOTE: G1 GC is only supported on JDK version 10 or later
# to use G1GC, uncomment the next two lines and update the version on the
# following three lines to your version of the JDK
# 10-13:-XX:-UseConcMarkSweepGC
# 10-13:-XX:-UseCMSInitiatingOccupancyOnly
14-:-XX:+UseG1GC

## JVM temporary directory
-Djava.io.tmpdir=${ES_TMPDIR}

## heap dumps

# generate a heap dump when an allocation from the Java heap fails; heap dumps
# are created in the working directory of the JVM unless an alternative path is
# specified
-XX:+HeapDumpOnOutOfMemoryError

# exit right after heap dump on out of memory error. Recommended to also use
# on java 8 for supported versions (8u92+).
9-:-XX:+ExitOnOutOfMemoryError

# specify an alternative path for heap dumps; ensure the directory exists and
# has sufficient space
默认情况下，Elasticsearch 将 JVM 配置为在内存不足异常时将堆转储到默认数据目录。
如果指定目录，JVM 将根据正在运行的实例的 PID 为堆转储生成文件名。
如果指定固定文件名而不是目录，那么当 JVM 需要对内存不足异常执行堆转储时，该文件必须不存在。否则，堆转储将失败。
-XX:HeapDumpPath=data

# 为JVM致命错误日志指定一个替代路径,缺省情况下，Elasticsearch 将 JVM 配置为将致命错误日志写入缺省日志记录目录
# 这些是 JVM 在遇到致命错误（如分段错误）时生成的日志。如果此路径不适合接收日志，请修改
-XX:ErrorFile=logs/hs_err_pid%p.log

## JDK 8 GC logging 日志记录
8:-XX:+PrintGCDetails
8:-XX:+PrintGCDateStamps
8:-XX:+PrintTenuringDistribution
8:-XX:+PrintGCApplicationStoppedTime
8:-Xloggc:logs/gc.log
8:-XX:+UseGCLogFileRotation
8:-XX:NumberOfGCLogFiles=32
8:-XX:GCLogFileSize=64m

# 默认情况下，Elasticsearch 启用垃圾回收 （GC） 日志。它们在jvm.options中配置，并输出到与 Elasticsearch 日志相同的默认位置。默认配置每 64 MB 轮换一次日志，最多可占用 2 GB 的磁盘空间。
# 通过使用一些示例选项进行创建，将默认 GC 日志输出位置更改为：/opt/my-app/gc.log$ES_HOME/config/jvm.options.d/gc.options
# 关闭所有以前的日志配置
-Xlog:disable

# Default settings from JEP 158, but with `utctime` instead of `uptime` to match the next line
-Xlog:all=warning:stderr:utctime,level,tags

# 使用各种选项在自定义位置启用GC日志记录  
-Xlog:gc*,gc+age=trace,safepoint:file=/opt/my-app/gc.log:utctime,pid,tags:filecount=32,filesize=64m

# JDK 9+ GC logging 日志记录
9-:-Xlog:gc*,gc+age=trace,safepoint:file=logs/gc.log:utctime,pid,tags:filecount=32,filesize=64m

```

