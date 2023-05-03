```shell
Linux查看系统中socket状态
当我们打开的socket数量很多时，netstat就会变得慢了，有什么办法可以快速查看系统中socket状态？

IPv4:

复制代码
$ cat /proc/net/sockstat

sockets: used 137
TCP: inuse 49 orphan 0 tw 3272 alloc 52 mem 46
UDP: inuse 1 mem 0
RAW: inuse 0
FRAG: inuse 0 memory 0
复制代码
说明：

sockets: used：已使用的所有协议套接字总量
TCP: inuse：正在使用（正在侦听）的TCP套接字数量。其值≤ netstat —lnt | grep ^tcp | wc —l
TCP: orphan：无主（不属于任何进程）的TCP连接数（无用、待销毁的TCP socket数）
TCP: tw：等待关闭的TCP连接数。其值等于netstat —ant | grep TIME_WAIT | wc —l
TCP：alloc(allocated)：已分配（已建立、已申请到sk_buff）的TCP套接字数量。其值等于netstat —ant | grep ^tcp | wc —l
TCP：mem：套接字缓冲区使用量（单位不详。用scp实测，速度在4803.9kB/s时：其值=11，netstat —ant 中相应的22端口的Recv-Q＝0，Send-Q≈400）
UDP：inuse：正在使用的UDP套接字数量
RAW：
FRAG：使用的IP段数量

IPv6：

$cat /proc/net/sockstat6

TCP6: inuse 3
UDP6: inuse 0
RAW6: inuse 0
FRAG6: inuse 0 memory 0
 

简单的说就是：

ipv4使用cat /proc/net/sockstat 
IPv6使用cat /proc/net/sockstat6

 

下面几个方法没有亲自用过：

$ ss [ OPTIONS ] [ STATE-FILTER ] [ ADDRESS-FILTER ] 
ss工具很强大，它的强大之处，大于可以设定过滤条件，我们可以根据socket的状态来进行过滤，也可通过端口与ip地址进行过滤。

 

netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'

按状态分组当前连接数

CLOSE_WAIT 92
ESTABLISHED 25

E系统实测:
[root@nw_cpees_vmapp02 ~]# netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}' 
LAST_ACK 3
SYN_RECV 2
CLOSE_WAIT 146
ESTABLISHED 5868
FIN_WAIT2 1
TIME_WAIT 34

 

引用：http://www.dewen.org/q/693


```

