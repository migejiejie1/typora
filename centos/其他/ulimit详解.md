```shell
# /etc/security/limits.conf

#<domain>        <type>  <item>  <value>

#<domain> can be:
#        - an user name //用户名
#        - a group name, with @group syntax //组名，带有@group语法
#        - the wildcard *, for default entry //通配符*，默认条目
#        - the wildcard %, can be also used with %group syntax, //通配符%，也可以和%组语法一起使用为maxlogin限制
#                 for maxlogin limit

#<type> can have the two values: //可以有两个值:
#        - "soft" for enforcing the soft limits //“soft”执行软限制
#        - "hard" for enforcing hard limits //“硬”表示执行硬限制

core -限制core文件的大小(KB)
date-最大数据大小(KB)
fsize-maximum文件大小(KB)
memlock-max内存锁定地址空间(KB)
nofile-max打开的文件数
rss -最大常驻集大小(KB)
stack-堆栈最大大小(KB)
cpu - cpu最大时间(分钟)
nproc -max进程数地址空间限制(KB)
maxsyslogins-该用户的最大登录数
priority——运行用户进程的优先级
locks -max用户可以持有的文件锁的数量
sigpending-max等待信号的数量
msgqueue-max POSIX消息队列所使用的内存(字节)
nice-max nice优先级允许提升为:[- 28,19]
rtprio -max实时优先级

========================================

core file size 		(blocks, -c) 	100
data seg size 		(kbytes, -d) 	unlimited
file size 			(blocks, -f) 	unlimited
pending signals 		(-i) 		2047
max locked memory 	(kbytes, -l) 	32
max memory size 		(kbytes, -m) 	unlimited
open files 		(-n) 		1024
pipe size 			(512 bytes, -p) 8
POSIX message queues 	(bytes, -q) 	819200
stack size 			(kbytes, -s) 	8192
cpu time 			(seconds, -t) 	unlimited
max user processes 		(-u) 		2047
virtual memory 		(kbytes, -v) 	unlimited
file locks 			(-x) 		unlimited


core file size core文件的最大值为100 blocks，
data seg size 进程的数据段可以任意大
file size 文件可以任意大
pending signals 最多有2047个待处理的信号
max locked memory 一个任务锁住的物理内存的最大值为32kB
max memory size 一个任务的常驻物理内存的最大值
open files 一个任务最多可以同时打开1024的文件
pipe size 管道的最大空间为4096字节
POSIX message queues POSIX的消息队列的最大值为819200字节
stack size 进程的栈的最大值为8192字节
cpu time 进程使用的CPU时间
max user processes 当前用户同时打开的进程(包括线程)的最大个数为2047
virtual memory 没有限制进程的最大地址空间
file locks 所能锁住的文件的最大个数没有限制

-a 　显示目前资源限制的设定。
-c <core文件上限> 　设定core文件的最大值，单位为区块。
-d <数据节区大小> 　程序数据节区的最大值，单位为KB。
-f <文件大小> 　shell所能建立的最大文件，单位为区块。
-H 　设定资源的硬性限制，也就是管理员所设下的限制。
-m <内存大小> 　指定可使用内存的上限，单位为KB。
-n <文件数目> 　指定同一时间最多可开启的文件数。
-p <缓冲区大小> 　指定管道缓冲区的大小，单位512字节。
-s <堆叠大小> 　指定堆叠的上限，单位为KB。
-S 　设定资源的弹性限制。
-t <CPU时间> 　指定CPU使用时间的上限，单位为秒。
-u <程序数目> 　用户最多可开启的程序数目。
-v <虚拟内存大小> 　指定可使用的虚拟内存上限，单位为KB。
```

