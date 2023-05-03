Linux netstat命令详解
netstat命令用于显示与IP、TCP、UDP和ICMP协议相关的统计数据，一般用于检验本机各端口的网络连接情况。netstat是在内核中访问网络及相关信息的程序，它能提供TCP连接，TCP和UDP监听，进程内存管理的相关报告。

```shell
[root@dxt-new-app7 ~]# netstat 
Active Internet connections (w/o servers)
Proto  Recv-Q   Send-Q         Local Address                              Foreign Address                             State      
tcp        0             0                    dxt-new-app7:54441                  1c58848876-hlc61.cn-bei:nfs      ESTABLISHED 
tcp        0             0                    dxt-new-app7:38578                  11f4a49073-kpm88.cn-bei:nfs   ESTABLISHED 
tcp        0             0                    dxt-new-app7:40537                  17b664b7f8-anu28.cn-bei:nfs   ESTABLISHED 
tcp        0             0                    dxt-new-app7:entextnetwk      10.50.128.66:22257                      TIME_WAIT   
tcp        1              0                    dxt-new-app7:entextnetwk      10.50.128.66:lm-x                         CLOSE_WAIT  

Active UNIX domain sockets (w/o servers)
Proto  RefCnt   Flags       Type                State                   I-Node             Path
unix     2              [ ]             DGRAM                                      7725                @/org/kernel/udev/udevd
unix     2              [ ]             DGRAM                                      9338                /var/run/portreserve/socket
unix     2              [ ]             DGRAM                                      10681              @/org/freedesktop/hal/udev_event
unix     3              [ ]             DGRAM                                     8449218          /dev/log
unix     2              [ ]             DGRAM                                      9006757 
unix     2              [ ]             STREAM      CONNECTED      3884285 
unix     2              [ ]             STREAM      CONNECTED      3884166 
unix     3              [ ]             STREAM      CONNECTED      30650              @/tmp/dbus-Yp7AqFpoq7

```

说明：

从整体上看，netstat的输出结果可以分为两个部分：

一个是Active Internet connections，称为有源TCP连接，其中"Recv-Q"和"Send-Q"指的是接收队列和发送队列。这些数字一般都应该是0。如果不是则表示软件包正在队列中堆积。这种情况只能在非常少的情况见到。

另一个是Active UNIX domain sockets，称为有源Unix域套接口(和网络套接字一样，但是只能用于本机通信，性能可以提高一倍)。

Proto显示连接使用的协议,RefCnt表示连接到本套接口上的进程号,Types显示套接口的类型,State显示套接口当前的状态,Path表示连接到套接口的其它进程使用的路径名。

```shell
套接口类型：

-t ：TCP

-u ：UDP

-raw ：RAW类型

--unix ：UNIX域类型

--ax25 ：AX25类型

--ipx ：ipx类型

--netrom ：netrom类型

状态说明：

LISTEN：侦听来自远方的TCP端口的连接请求

SYN-SENT：再发送连接请求后等待匹配的连接请求（如果有大量这样的状态包，检查是否中招了）

SYN-RECEIVED：再收到和发送一个连接请求后等待对方对连接请求的确认（如有大量此状态，估计被flood攻击了）

ESTABLISHED：代表一个打开的连接

FIN-WAIT-1：等待远程TCP连接中断请求，或先前的连接中断请求的确认

FIN-WAIT-2：从远程TCP等待连接中断请求

CLOSE-WAIT：等待从本地用户发来的连接中断请求

CLOSING：等待远程TCP对连接中断的确认

LAST-ACK：等待原来的发向远程TCP的连接中断请求的确认（不是什么好东西，此项出现，检查是否被攻击）

TIME-WAIT：等待足够的时间以确保远程TCP接收到连接中断请求的确认

CLOSED：没有任何连接状态
```

