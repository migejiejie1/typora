```shell
一，故障描述：
从昨天开始，在值班群中陆续值班人员反映系统后台存在卡顿问题，如下图：
记一次由tcp_tw_recycle参数引发的血案
而且在卡顿的同时登陆服务器也会卡好久。此现象只在一台服务器有出现。

二，故障分析：
1，登陆服务器查看资源使用top，vmstat等命令查看了一番发现服务器各项指标都没有异常。于是将问题转向了网络层。
2，客户端端值班人员反映只有在访问系统后台的时候才会出现卡顿，访问其他网站正常。
3，本地使用ping服务器外网ip正常返回，无丢包，延迟也正常。
4，使用http-ping工具时，问题出现了，会经常性的出现连接失败：
(http-ping工具下载地址https://www.softpedia.com/get/Network-Tools/IP-Tools/http-ping.shtml)
记一次由tcp_tw_recycle参数引发的血案
为什么会连接失败呢？
5，使用tcpdump抓包在卡顿的时候会抓到大量的syn请求，但服务器没有响应：
记一次由tcp_tw_recycle参数引发的血案
6，登录服务器查看tcp相关数据

$ netstat -s | grep -i listen
    326 times the listen queue of a socket overflowed
    751346 SYNs to LISTEN sockets dropped
发现在卡顿时有大量tcp syn包被丢弃，数值一直在增长。

三，故障处理：
在查阅资料并结合实际情况后，发现该服务器同时启用了 tcp_timestamps和tcp_tw_recycle参数。
后想起，之前同事为改善time_wait连接数过多问题曾改过该内核参数。
解决办法是，关闭tcp_tw_recycle：

$ vi  /etc/sysctl.conf
#修改为如下
net.ipv4.tcp_tw_recycle = 0
#保存退出，使之生效
$ sysctl -p
再观察，发现服务已正常，卡顿现象消失。

四，总结：
我们先来man一下这两个参数(man tcp)：

 tcp_timestamps (Boolean; default: enabled; since Linux 2.2)
              Enable RFC 1323 TCP timestamps.
tcp_timestamp 是 RFC1323 定义的优化选项，主要用于 TCP 连接中 RTT(Round Trip Time) 的计算，开启 tcp_timestamp 有利于系统计算更加准确的 RTT，也就有利于 TCP 性能的提升。（默认开启）
关于tcp_timestamps详情请见：https://tools.ietf.org/pdf/rfc7323.pdf

 tcp_tw_recycle (Boolean; default: disabled; since Linux 2.4)
              Enable fast recycling of TIME_WAIT sockets.  Enabling this option is not recommended since this causes problems when working with NAT (Network Address
              Translation).
开启tcp_tw_recycle会启用tcp time_wait的快速回收，这个参数不建议在NAT环境中启用，它会引起相关问题。

tcp_tw_recycle是依赖tcp_timestamps参数的，在一般网络环境中，可能不会有问题，但是在NAT环境中，问题就来了。比如我遇到的这个情况，办公室的外网地址只有一个，所有人访问后台都会通过路由器做SNAT将内网地址映射为公网IP，由于服务端和客户端都启用了tcp_timestamps，因此TCP头部中增加时间戳信息，而在服务器看来，同一客户端的时间戳必然是线性增长的，但是，由于我的客户端网络环境是NAT，因此每台主机的时间戳都是有差异的，在启用tcp_tw_recycle后，一旦有客户端断开连接，服务器可能就会丢弃那些时间戳较小的客户端的SYN包，这也就导致了网站访问极不稳定。

主机A SIP:P1 (时间戳T0) ---> Server 主机A断开后
主机B SIP:P1 (时间戳T2) T2 < T0 ---> Server 丢弃

经过此次故障，告诫我们在处理线上问题时，不能盲目修改参数，一定要经过测试，确认无误后，再应用于生产环境。同时，也要加深对相关内核参数的认识和理解。
```

