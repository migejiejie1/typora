```shell
jstat
相关命令：暂无相关命令
Jstat是JDK自带的一个轻量级小工具。全称“Java Virtual Machine statistics monitoring tool”，它位于java的bin目录下，主要利用JVM内建的指令对Java应用程序的资源和性能进行实时的命令行的监控，包括了对Heap size和垃圾回收状况的监控。可见，Jstat是轻量级的、专门针对JVM的工具，非常适用。

jstat工具特别强大，有众多的可选项，详细查看堆内各个部分的使用量，以及加载类的数量。使用时，需加上查看进程的进程id，和所选参数。参考格式如下：

选项option代表这用户希望查询的虚拟机信息，主要分为3类：类装载、垃圾收集和运行期编译状况.

–class 监视类装载、卸载数量、总空间及类装载所耗费的时间
–gc 监视Java堆状况，包括Eden区、2个Survivor区、老年代、永久代等的容量
–gccapacity 监视内容与-gc基本相同，但输出主要关注Java堆各个区域使用到的最大和最小空间
–gcutil 监视内容与-gc基本相同，但输出主要关注已使用空间占总空间的百分比
–gccause 与-gcutil功能一样，但是会额外输出导致上一次GC产生的原因
–gcnew 监视新生代GC的状况
–gcnewcapacity 监视内容与-gcnew基本相同，输出主要关注使用到的最大和最小空间
–gcold 监视老年代GC的状况
–gcoldcapacity 监视内容与-gcold基本相同，输出主要关注使用到的最大和最小空间
–gcpermcapacity 输出永久代使用到的最大和最小空间
–compiler 输出JIT编译器编译过的方法、耗时等信息
–printcompilation 输出已经被JIT编译的方法


===========================================================

-class 显示加载class的数量，及所占空间等信息
 

$ jstat -class  15224  1000 10                 
Loaded  Bytes  Unloaded  Bytes     Time   
  3911  8184.6        0     0.0       1.98

Loaded 装载的类的数量
Bytes 装载类所占用的字节数
Unloaded 卸载类的数量
Bytes 卸载类的字节数
Time 装载和卸载类所花费的时间

===========================================================
-gcutil 统计gc信息
 

$ jstat -gcutil 5061  1000 2000     
  S0     S1     E      O      P     YGC     YGCT    FGC    FGCT     GCT   
100.00   0.00  86.30  33.52  99.96     30    6.625     0    0.000    6.625


S0 年轻代中第一个survivor（幸存区）已使用的占当前容量百分比
S1 年轻代中第二个survivor（幸存区）已使用的占当前容量百分比
E 年轻代中Eden（伊甸园）已使用的占当前容量百分比
O old代已使用的占当前容量百分比
P perm代已使用的占当前容量百分比
YGC 从应用程序启动到采样时年轻代中gc次数
YGCT 从应用程序启动到采样时年轻代中gc所用时间(s)
FGC 从应用程序启动到采样时old代(全gc)gc次数
FGCT 从应用程序启动到采样时old代(全gc)gc所用时间(s)
GCT 从应用程序启动到采样时gc用的总时间(s)

==========================================================
-gc
 

$ jstat -gc 12788  1000 20   
 S0C    S1C    S0U    S1U      EC       EU        OC         OU       PC     PU    YGC     YGCT    FGC    FGCT     GCT   
32768.0 32768.0  0.0   8548.4 196608.0 41343.6  3932160.0     0.0     21248.0 14154.8      1    0.064   0      0.000    0.064

S0C 年轻代中第一个survivor（幸存区）的容量 (字节)
S1C 年轻代中第二个survivor（幸存区）的容量 (字节)
S0U 年轻代中第一个survivor（幸存区）目前已使用空间 (字节)
S1U 年轻代中第二个survivor（幸存区）目前已使用空间 (字节)
EC 年轻代中Eden（伊甸园）的容量 (字节)
EU 年轻代中Eden（伊甸园）目前已使用空间 (字节)
OC Old代的容量 (字节)
OU Old代目前已使用空间 (字节)
PC Perm(持久代)的容量 (字节)
PU Perm(持久代)目前已使用空间 (字节)
YGC 从应用程序启动到采样时年轻代中gc次数
YGCT 从应用程序启动到采样时年轻代中gc所用时间(s)
FGC 从应用程序启动到采样时old代(全gc)gc次数
FGCT 从应用程序启动到采样时old代(全gc)gc所用时间(s)
GCT 从应用程序启动到采样时gc用的总时间(s)

==========================================================
-gccapacity
 

$ jstat -gccapacity 15224  1000 10 
 NGCMN    NGCMX     NGC     S0C   S1C       EC      OGCMN      OGCMX       OGC         OC      PGCMN    PGCMX     PGC       PC     YGC    FGC 
2097152.0 2097152.0 2097152.0 262144.0 262144.0 1572864.0  2097152.0  2097152.0  2097152.0  2097152.0  21248.0 262144.0  41872.0  41872.0      8     2

NGCMN 年轻代(young)中初始化(最小)的大小(字节)
NGCMX 年轻代(young)的最大容量 (字节)
NGC 年轻代(young)中当前的容量 (字节)
S0C 年轻代中第一个survivor（幸存区）的容量 (字节)
S1C 年轻代中第二个survivor（幸存区）的容量 (字节)
EC 年轻代中Eden（伊甸园）的容量 (字节)
OGCMN old代中初始化(最小)的大小 (字节)
OGCMX old代的最大容量(字节)
OGC old代当前新生成的容量 (字节)
OC Old代的容量 (字节)
PGCMN perm代中初始化(最小)的大小 (字节)
PGCMX perm代的最大容量 (字节)
PGC perm代当前新生成的容量 (字节)
PC Perm(持久代)的容量 (字节)
YGC 从应用程序启动到采样时年轻代中gc次数
FGC 从应用程序启动到采样时old代(全gc)gc次数

===================================================================
gcnew
 

$ jstat -gcnew  15224  1000 10            
 S0C    S1C    S0U    S1U   TT MTT  DSS      EC       EU     YGC     YGCT  
262144.0 262144.0 239923.8    0.0  1  15 131072.0 1572864.0 394048.3     12   12.207

S0C 年轻代中第一个survivor（幸存区）的容量 (字节)
S1C 年轻代中第二个survivor（幸存区）的容量 (字节)
S0U 年轻代中第一个survivor（幸存区）目前已使用空间 (字节)
S1U 年轻代中第二个survivor（幸存区）目前已使用空间 (字节)
TT 持有次数限制
MTT 最大持有次数限制
EC 年轻代中Eden（伊甸园）的容量 (字节)
EU 年轻代中Eden（伊甸园）目前已使用空间 (字节)
YGC 从应用程序启动到采样时年轻代中gc次数
YGCT 从应用程序启动到采样时年轻代中gc所用时间(s)
FGC 从应用程序启动到采样时old代(全gc)gc次数

=======================================================================
gcnewcapacity
 

$ jstat -gcnewcapacity  15224  1000 10       
  NGCMN      NGCMX       NGC      S0CMX     S0C     S1CMX     S1C       ECMX        EC      YGC   FGC 
 2097152.0  2097152.0  2097152.0 262144.0 262144.0 262144.0 262144.0  1572864.0  1572864.0    16    10

NGCMN 年轻代(young)中初始化(最小)的大小(字节)
NGCMX 年轻代(young)的最大容量 (字节)
NGC 年轻代(young)中当前的容量 (字节)
S0CMX 年轻代中第一个survivor（幸存区）的最大容量 (字节)
S0C 年轻代中第一个survivor（幸存区）的容量 (字节)
S1CMX 年轻代中第二个survivor（幸存区）的最大容量 (字节)
S1C 年轻代中第二个survivor（幸存区）的容量 (字节)
ECMX 年轻代中Eden（伊甸园）的最大容量 (字节) EC 年轻代中
Eden（伊甸园）的容量 (字节)
YGC 从应用程序启动到采样时年轻代中gc次数
FGC 从应用程序启动到采样时old代(全gc)gc次数

=======================================================================
gcold old代对象的信息
 

$ jstat -gcold  15224  1000 10                
   PC       PU        OC          OU       YGC    FGC    FGCT     GCT   
 42064.0  25167.2   2097152.0   1183624.3     19    14    2.091   19.320
 
PC Perm(持久代)的容量 (字节)
PU Perm(持久代)目前已使用空间 (字节)
OC Old代的容量 (字节)
OU Old代目前已使用空间 (字节)
YGC 从应用程序启动到采样时年轻代中gc次数
FGC 从应用程序启动到采样时old代(全gc)gc次数
FGCT 从应用程序启动到采样时old代(全gc)gc所用时间(s)
GCT 从应用程序启动到采样时gc用的总时间(s)
=======================================================================
-printcompilation 当前VM执行的信息
 

$ stat -printcompilation  15224  1000 10               
Compiled  Size  Type Method
     532    517    1 java/util/concurrent/locks/AbstractQueuedSynchronizer acquire
     
Compiled 编译任务的数目
Size 方法生成的字节码的大小
Type 编译类型
Method 类名和方法名用来标识编译的方法。类名使用/做为一个命名空间分隔符。方法名是给定类中的方法。上述格式是由-XX:+PrintComplation选项进行设置的
```

