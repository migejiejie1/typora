```shell
keepalived的配置详解（非常详细）

﻿
转载自：http://blog.csdn.net/u010391029/article/details/48311699
1. 前言

VRRP(Virtual Router Redundancy Protocol)协议是用于实现路由器冗余的协议，最新协议在RFC3768中定义，原来的定义RFC2338被废除，新协议相对还简化了一些功能。

2. 协议说明

2.1 协议

VRRP协议是为消除在静态缺省路由环境下的缺省路由器单点故障引起的网络失效而设计的主备模式的协议，使得在发生故障而进行设备功能切换时可以不影响内外数据通信，不需要再修改内部网络的网络参数。VRRP协议需要具有IP地址备份，优先路由选择，减少不必要的路由器间通信等功能。

VRRP协议将两台或多台路由器设备虚拟成一个设备，对外提供虚拟路由器IP(一个或多个)，而在路由器组内部，如果实际拥有这个对外IP的路由器如果工作正常的话就是MASTER，或者是通过算法选举产生，MASTER实现针对虚拟路由器IP的各种网络功能，如ARP请求，ICMP，以及数据的转发等；其他设备不拥有该IP，状态是BACKUP，除了接收MASTER的VRRP状态通告信息外，不执行对外的网络功能。当主机失效时，BACKUP将接管原先MASTER的网络功能。

配置VRRP协议时需要配置每个路由器的虚拟路由器ID(VRID)和优先权值，使用VRID将路由器进行分组，具有相同VRID值的路由器为同一个组，VRID是一个0～255的正整数；同一组中的路由器通过使用优先权值来选举MASTER，优先权大者为MASTER，优先权也是一个0～255的正整数。

VRRP协议使用多播数据来传输VRRP数据，VRRP数据使用特殊的虚拟源MAC地址发送数据而不是自身网卡的MAC地址，VRRP运行时只有MASTER路由器定时发送VRRP通告信息，表示MASTER工作正常以及虚拟路由器IP(组)，BACKUP只接收VRRP数据，不发送数据，如果一定时间内没有接收到MASTER的通告信息，各BACKUP将宣告自己成为MASTER，发送通告信息，重新进行MASTER选举状态。

2.2 MASTER选举
如果对外的虚拟路由器IP就是路由器本身配置的IP地址的话，该路由器始终都是MASTER；
否则如果不具备虚拟IP的话，将进行MASTER选举，各路由器都宣告自己是MASTER，发送VRRP通告信息；
如果收到其他机器的发来的通告信息的优先级比自己高，将转回BACKUP状态；
如果优先级相等的话，将比较路由器的实际IP，IP值较大的优先权高；
不过如果对外的虚拟路由器IP就是路由器本身的IP的话，该路由器始终将是MASTER，这时的优先级值为255。

2.3 协议状态机

VRRP协议状态比较简单，就三种状态，初始化，主机，备份机。
                      +---------------+
           +--------->|               |<-------------+
           |          |  Initialize   |              |
           |   +------|               |----------+   |
           |   |      +---------------+          |   |
           |   |                                 |   |
           |   V                                 V   |
   +---------------+                       +---------------+
   |               |---------------------->|               |
   |    Master     |                       |    Backup     |
   |               |<----------------------|               |
   +---------------+                       +---------------+
复制代码

初始化：
路由器启动时，如果路由器的优先级是255(最高优先级，路由器拥有路由器地址)，要发送VRRP通告信息，并发送广播ARP信息通告路由器IP地址对应的MAC地址为路由虚拟MAC，设置通告信息定时器准备定时发送VRRP通告信息，转为MASTER状态；
否则进入BACKUP状态，设置定时器检查定时检查是否收到MASTER的通告信息。

主机：
主机状态下的路由器要完成如下功能：
设置定时通告定时器；
用VRRP虚拟MAC地址响应路由器IP地址的ARP请求；
转发目的MAC是VRRP虚拟MAC的数据包；
如果是虚拟路由器IP的拥有者，将接受目的地址是虚拟路由器IP的数据包，否则丢弃；
当收到shutdown的事件时删除定时通告定时器，发送优先权级为0的通告包，转初始化状态；
如果定时通告定时器超时时，发送VRRP通告信息；
收到VRRP通告信息时，如果优先权为0，发送VRRP通告信息；否则判断数据的优先级是否高于本机，或相等而且实际IP地址大于本地实际IP，设置定时通告定时器，复位主机超时定时器，转BACKUP状态；否则的话，丢弃该通告包；

备机：
备机状态下的路由器要实现以下功能：
设置主机超时定时器；
不能响应针对虚拟路由器IP的ARP请求信息；
丢弃所有目的MAC地址是虚拟路由器MAC地址的数据包；
不接受目的是虚拟路由器IP的所有数据包；
当收到shutdown的事件时删除主机超时定时器，转初始化状态；
主机超时定时器超时的时候，发送VRRP通告信息，广播ARP地址信息，转MASTER状态；
收到VRRP通告信息时，如果优先权为0，表示进入MASTER选举；否则判断数据的优先级是否高于本机，如果高的话承认MASTER有效，复位主机超时定时器；否则的话，丢弃该通告包；

2.4 ARP查询处理

当内部主机通过ARP查询虚拟路由器IP地址对应的MAC地址时，MASTER路由器回复的MAC地址为虚拟的VRRP的MAC地址，而不是实际网卡的MAC地址，这样在路由器切换时让内网机器觉察不到；而在路由器重新启动时，不能主动发送本机网卡的实际MAC地址。如果虚拟路由器开启的ARP代理(proxy_arp)功能，代理的ARP回应也回应VRRP虚拟MAC地址；

2.5 VRRP应用举例

            +-----------+      +-----------+
            |   Rtr1    |      |   Rtr2    |
            |(MR VRID=1)|      |(BR VRID=1)|
            |(BR VRID=2)|      |(MR VRID=2)|
    VRID=1  +-----------+      +-----------+  VRID=2
    IP A ---------->*            *<---------- IP B
                    |            |
                    |            |
  ------------------+------------+-----+--------+--------+--------+--
                                       ^        ^        ^        ^
                                       |        |        |        |
                                     (IP A)   (IP A)   (IP B)   (IP B)
                                       |        |        |        |
                                    +--+--+  +--+--+  +--+--+  +--+--+
                                    |  H1 |  |  H2 |  |  H3 |  |  H4 |
                                    +-----+  +-----+  +--+--+  +--+--+
     Legend:
              ---+---+---+--  =  Ethernet, Token Ring, or FDDI
                           H  =  Host computer
                          MR  =  Master Router
                          BR  =  Backup Router
                           *  =  IP Address
                        (IP)  =  default router for hosts
复制代码

这是通常VRRP使用拓扑，两台路由器运行VRRP互为备份，路由器1作为VRID组1的MASTER，IP地址A，VRID组2的BACKUP，路由器2作为VRID组2的MASTER，IP地址B，VRID组1的BACKUP，内部网络中一部分机器的缺省网关地址是IP地址A，一部分是IP地址B，正常情况下以A为网关的数据将走路由器1，以B为网关的数据将走路由器2，如果一台路由器发生故障，所有数据将走另一台路由器。

3. 协议定义

3.1 以太头

源MAC地址必须为虚拟MAC地址：00-00-5E-00-01-{VRID}，VRID为虚拟路由器ID值，16进制格式，所以同一网段中最多有255个VRRP路由器；目的MAC为多播类型的MAC。

这里可以看出VRID非常重要


3.2 IP头参数

VRRP包的源地址是本机地址，目的地址必须为224.0.0.18，为一多播地址；IP协议号为112；IP包的TTL值必须为255。

3.3 VRRP协议数据格式
    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |Version| Type  | Virtual Rtr ID|   Priority    | Count IP Addrs|
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |   Auth Type   |   Adver Int   |          Checksum             |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                         IP Address (1)                        |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                            .                                  |
   |                            .                                  |
   |                            .                                  |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                         IP Address (n)                        |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                     Authentication Data (1)                   |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                     Authentication Data (2)                   |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
复制代码



其中：
version：版本，4位，在RFC3768中定义为2；
Type：类型，4位，目前只定义一种类类型：通告数据，取值为1；
Virtual Rtr ID：虚拟路由器ID，8位
Priority：优先级，8位，具备冗余IP地址的设备的优先级为255；
Count IP Addrs：VRRP包中的IP地址数量，8位；
Auth Type：认证类型，8位，RFC3768中认证功能已经取消，此字段值定义0(不认证)，为1，2只作为对老版本的兼容；
Adver Int：通告包的发送间隔时间，8位，单位是秒，缺省是1秒；
Checksum：校验和，16位，校验数据范围只是VRRP数据，即从VRRP的版本字段开始的数据，不包括IP头；
IP Address(es)：和虚拟路由器相关的IP地址，数量由Count IP Addrs决定
Authentication Data：RFC3768中定义该字段只是为了和老版本兼容，必须置0。

3.4 接收数据时的必须检查

收到VRRP数据包时要进行以下验证，不满足的数据包将被丢弃：
   -  TTL必须为255；
   -  VRRP版本号必须为2；
   -  一个包中数据字段必须完整；
   -  校验和必须正确；
   -  必须验证在接收的网卡上配置了VRID值，而且本地路由器不是路由IP地址的拥有者
   -  必须验证VVRP认证类型和配置的一致；


4. 结论

VRRP实现了对路由器IP地址的冗余功能，防止了单点故障造成的网络失效，VRRP本身是热备形式的，但可以通过互相热备实现路由器的均衡处理，新版的VRRP较老版简化了认证处理，实际不再进行数据的认证，这是因为在实际应用中经常出现认证成为造成多个MASTER同时使用的异常情况。



Keepalived原理与实战精讲

什么是Keepalived呢，keepalived观其名可知，保持存活，在网络里面就是保持在线了，也就是所谓的高可用或热备，用来防止单点故障(单点故障是指一旦某一点出现故障就会导致整个系统架构的不可用)的发生，那说到keepalived时不得不说的一个协议就是VRRP协议，可以说这个协议就是keepalived实现的基础，那么首先我们来看看VRRP协议

注：搞运维的要有足够的耐心哦，不理解协议就很难透彻的掌握keepalived的了

一，VRRP协议
VRRP协议
学过网络的朋友都知道，网络在设计的时候必须考虑到冗余容灾，包括线路冗余，设备冗余等，防止网络存在单点故障，那在路由器或三层交换机处实现冗余就显得尤为重要，在网络里面有个协议就是来做这事的，这个协议就是VRRP协议，Keepalived就是巧用VRRP协议来实现高可用性(HA)的

VRRP协议有一篇文章写的非常好，大家可以直接看这里(记得认真看看哦，后面基本都已这个为基础的了)
帖子地址：http://bbs.ywlm.net/thread-790-1-1.html
只需要把服务器当作路由器即可！

在《VRRP协议》里讲到了虚拟路由器的ID也就是VRID在这里比较重要

keepalived完全遵守VRRP协议，包括竞选机制等等

二，Keepalived原理

Keepalived原理
keepalived也是模块化设计，不同模块复杂不同的功能，下面是keepalived的组件
core check vrrp libipfwc libipvs-2.4 libipvs-2.6

core：是keepalived的核心，复杂主进程的启动和维护，全局配置文件的加载解析等
check：负责healthchecker(健康检查)，包括了各种健康检查方式，以及对应的配置的解析包括LVS的配置解析
vrrp：VRRPD子进程，VRRPD子进程就是来实现VRRP协议的
libipfwc：iptables(ipchains)库，配置LVS会用到
libipvs*：配置LVS会用到
注意，keepalived和LVS完全是两码事，只不过他们各负其责相互配合而已


 

keepalived启动后会有三个进程
父进程：内存管理，子进程管理等等
子进程：VRRP子进程
子进程：healthchecker子进程

有图可知，两个子进程都被系统WatchDog看管，两个子进程各自复杂自己的事，healthchecker子进程复杂检查各自服务器的健康程度，例如HTTP，LVS等等，如果healthchecker子进程检查到MASTER上服务不可用了，就会通知本机上的兄弟VRRP子进程，让他删除通告，并且去掉虚拟IP，转换为BACKUP状态

三，Keepalived配置文件详解


keepalived配置详解
keepalived有三类配置区域(姑且就叫区域吧)，注意不是三种配置文件，是一个配置文件里面三种不同类别的配置区域

全局配置(Global Configuration)
VRRPD配置
LVS配置

一，全局配置
全局配置又包括两个子配置：
全局定义(global definition)
静态路由配置(static ipaddress/routes)

1，全局定义(global definition)配置范例
global_defs
{
notification_email
{
admin@example.com
}
notification_email_from admin@example.com
smtp_server 127.0.0.1
stmp_connect_timeout 30
router_id node1
}
复制代码
全局配置解析
global_defs全局配置标识，表面这个区域{}是全局配置
notification_email

{

admin@example.com
admin@ywlm.net

}
复制代码
表示keepalived在发生诸如切换操作时需要发送email通知，以及email发送给哪些邮件地址，邮件地址可以多个，每行一个

notification_email_from admin@example.com
表示发送通知邮件时邮件源地址是谁

smtp_server 127.0.0.1
表示发送email时使用的smtp服务器地址，这里可以用本地的sendmail来实现

smtp_connect_timeout 30
连接smtp连接超时时间

router_id node1
机器标识

2，静态地址和路由配置范例
static_ipaddress
{
192.168.1.1/24 brd + dev eth0 scope global
192.168.1.2/24 brd + dev eth1 scope global
}
static_routes
{
src $SRC_IP to $DST_IP dev $SRC_DEVICE
src $SRC_IP to $DST_IP via $GW dev $SRC_DEVICE
}
复制代码

这里实际上和系统里面命令配置IP地址和路由一样例如：
192.168.1.1/24 brd + dev eth0 scope global 相当于: ip addr add 192.168.1.1/24 brd + dev eth0 scope global
就是给eth0配置IP地址
路由同理
一般这个区域不需要配置
这里实际上就是给服务器配置真实的IP地址和路由的，在复杂的环境下可能需要配置，一般不会用这个来配置，我们可以直接用vi /etc/sysconfig/network-script/ifcfg-eth1来配置，切记这里可不是VIP哦，不要搞混淆了，切记切记！

二，VRRPD配置
VRRPD配置包括三个类
VRRP同步组(synchroization group)
VRRP实例(VRRP Instance)VRRP脚本

1，VRRP同步组(synchroization group)配置范例
vrrp_sync_group VG_1 {
group {
http
mysql
}
notify_master /path/to/to_master.sh
notify_backup /path_to/to_backup.sh
notify_fault "/path/fault.sh VG_1"
notify /path/to/notify.sh
smtp_alert
}
复制代码
其中：
group {
http
mysql
}
复制代码
http和mysql是实例名和下面的实例名一致

notify_master /path/to/to_master.sh：表示当切换到master状态时，要执行的脚本

notify_backup /path_to/to_backup.sh：表示当切换到backup状态时，要执行的脚本

notify_fault "/path/fault.sh VG_1"
复制代码
notify /path/to/notify.sh：

smtp alter表示切换时给global defs中定义的邮件地址发送邮件通知（在这里我们可以利用一款非常小巧的邮件发送工具mailx来进行邮件的发送工作）

2，VRRP实例(instance)配置范例
vrrp_instance http {
state MASTER
interface eth0
dont_track_primary
track_interface {
eth0
eth1
}
mcast_src_ip <IPADDR>
garp_master_delay 10
virtual_router_id 51
priority 100
advert_int 1
authentication {
auth_type PASS
autp_pass 1234
}
virtual_ipaddress {
#<IPADDR>/<MASK> brd <IPADDR> dev <STRING> scope <SCOPT> label <LABEL>
192.168.200.17/24 dev eth1
192.168.200.18/24 dev eth2 label eth2:1
}
virtual_routes {
# src <IPADDR> [to] <IPADDR>/<MASK> via|gw <IPADDR> dev <STRING> scope <SCOPE> tab
src 192.168.100.1 to 192.168.109.0/24 via 192.168.200.254 dev eth1
192.168.110.0/24 via 192.168.200.254 dev eth1
192.168.111.0/24 dev eth2
192.168.112.0/24 via 192.168.100.254
}
nopreempt
preemtp_delay 300
debug
}
复制代码

state：state指定instance(Initial)的初始状态，就是说在配置好后，这台服务器的初始状态就是这里指定的，但这里指定的不算，还是得要通过竞选通过优先级来确定，里如果这里设置为master，但如若他的优先级不及另外一台，那么这台在发送通告时，会发送自己的优先级，另外一台发现优先级不如自己的高，那么他会就回抢占为master

interface：实例绑定的网卡，因为在配置虚拟IP的时候必须是在已有的网卡上添加的

dont track primary：忽略VRRP的interface错误

track interface：跟踪接口，设置额外的监控，里面任意一块网卡出现问题，都会进入故障(FAULT)状态，例如，用nginx做均衡器的时候，内网必须正常工作，如果内网出问题了，这个均衡器也就无法运作了，所以必须对内外网同时做健康检查

mcast src ip：发送多播数据包时的源IP地址，这里注意了，这里实际上就是在那个地址上发送VRRP通告，这个非常重要，一定要选择稳定的网卡端口来发送，这里相当于heartbeat的心跳端口，如果没有设置那么就用默认的绑定的网卡的IP，也就是interface指定的IP地址

garp master delay：在切换到master状态后，延迟进行免费的ARP(gratuitous ARP)请求

virtual router id：这里设置VRID，这里非常重要，相同的VRID为一个组，他将决定多播的MAC地址

priority 100：设置本节点的优先级，优先级高的为master

advert int：检查间隔，默认为1秒

virtual ipaddress：这里设置的就是VIP，也就是虚拟IP地址，他随着state的变化而增加删除，当state为master的时候就添加，当state为backup的时候删除，这里主要是有优先级来决定的，和state设置的值没有多大关系，这里可以设置多个IP地址

virtual routes：原理和virtual ipaddress一样，只不过这里是增加和删除路由

lvs sync daemon interface：lvs syncd绑定的网卡

authentication：这里设置认证

auth type：认证方式，可以是PASS或AH两种认证方式

auth pass：认证密码

nopreempt：设置不抢占，这里只能设置在state为backup的节点上，而且这个节点的优先级必须别另外的高。当主mysql恢复后不抢占资源

preempt delay：抢占延迟

debug：debug级别

notify master：和sync group这里设置的含义一样，可以单独设置，例如不同的实例通知不同的管理人员，http实例发给网站管理员，mysql的就发邮件给DBA

3，VRRP脚本

vrrp_script check_running {
   script "/usr/local/bin/check_running"
   interval 10
   weight 10
}

vrrp_instance http {
   state BACKUP
   smtp_alert
   interface eth0
   virtual_router_id 101
   priority 90
   advert_int 3
   authentication {
   auth_type PASS
   auth_pass whatever
   }
   virtual_ipaddress {
   1.1.1.1
   }
   track_script {
   check_running weight 20
   }
}
复制代码

首先在vrrp_script区域定义脚本名字和脚本执行的间隔和脚本执行的优先级变更
vrrp_script check_running {
script "/usr/local/bin/check_running"
interval 10     #脚本执行间隔
weight 10      #脚本结果导致的优先级变更：10表示优先级+10；-10则表示优先级-10
}
然后在实例(vrrp_instance)里面引用，有点类似脚本里面的函数引用一样：先定义，后引用函数名
track_script {
check_running weight 20
}

注意：VRRP脚本(vrrp_script)和VRRP实例(vrrp_instance)属于同一个级别
LVS配置

如果你没有配置LVS+keepalived那么无需配置这段区域，里如果你用的是nginx来代替LVS，这无限配置这款，这里的LVS配置是专门为keepalived+LVS集成准备的。
注意了，这里LVS配置并不是指真的安装LVS然后用ipvsadm来配置他，而是用keepalived的配置文件来代替ipvsadm来配置LVS，这样会方便很多，一个配置文件搞定这些，维护方便，配置方便是也！

这里LVS配置也有两个配置
一个是虚拟主机组配置
一个是虚拟主机配置

1，虚拟主机组配置文件详解
这个配置是可选的，根据需求来配置吧，这里配置主要是为了让一台realserver上的某个服务可以属于多个Virtual Server，并且只做一次健康检查

virtual_server_group <STRING> {
# VIP port
<IPADDR> <PORT>
<IPADDR> <PORT>
fwmark <INT>
}

2，虚拟主机配置

virtual server可以以下面三种的任意一种来配置
1. virtual server IP port
2. virtual server fwmark int
3. virtual server group string
复制代码
下面以第一种比较常用的方式来配详细解说一下

virtual_server 192.168.1.2 80 {                     #设置一个virtual server: VIP:Vport
delay_loop 3                                                  # service polling的delay时间，即服务轮询的时间间隔

lb_algo rr|wrr|lc|wlc|lblc|sh|dh                        #LVS调度算法
lb_kind NAT|DR|TUN                                      #LVS集群模式                      
persistence_timeout 120                                #会话保持时间（秒为单位），即以用户在120秒内被分配到同一个后端realserver
persistence_granularity <NETMASK>              #LVS会话保持粒度，ipvsadm中的-M参数，默认是0xffffffff，即每个客户端都做会话保持
protocol TCP                                                  #健康检查用的是TCP还是UDP
ha_suspend                                                   #suspendhealthchecker’s activity
virtualhost <string>                                       #HTTP_GET做健康检查时，检查的web服务器的虚拟主机（即host：头）

sorry_server <IPADDR> <PORT>                 #备用机，就是当所有后端realserver节点都不可用时，就用这里设置的，也就是临时把所有的请求都发送到这里啦

real_server <IPADDR> <PORT>                    #后端真实节点主机的权重等设置，主要，后端有几台这里就要设置几个
{
weight 1                                                         #给每台的权重，0表示失效(不知给他转发请求知道他恢复正常)，默认是1
inhibit_on_failure                                            #表示在节点失败后，把他权重设置成0，而不是冲IPVS中删除

notify_up <STRING> | <QUOTED-STRING>  #检查服务器正常(UP)后，要执行的脚本
notify_down <STRING> | <QUOTED-STRING> #检查服务器失败(down)后，要执行的脚本

HTTP_GET                                                     #健康检查方式
{
url {                                                                #要坚持的URL，可以有多个
path /                                                             #具体路径
digest <STRING>                                            
status_code 200                                            #返回状态码
}
connect_port 80                                            #监控检查的端口

bindto <IPADD>                                             #健康检查的IP地址
connect_timeout   3                                       #连接超时时间
nb_get_retry 3                                               #重连次数
delay_before_retry 2                                      #重连间隔
} # END OF HTTP_GET|SSL_GET


#下面是常用的健康检查方式，健康检查方式一共有HTTP_GET|SSL_GET|TCP_CHECK|SMTP_CHECK|MISC_CHECK这些
#TCP方式
TCP_CHECK {
connect_port 80
bindto 192.168.1.1
connect_timeout 4
} # TCP_CHECK

# SMTP方式，这个可以用来给邮件服务器做集群
SMTP_CHECK
host {
connect_ip <IP ADDRESS>
connect_port <PORT>                                     #默认检查25端口
14 KEEPALIVED
bindto <IP ADDRESS>
}
connect_timeout <INTEGER>
retry <INTEGER>
delay_before_retry <INTEGER>
# "smtp HELO"ž|·-ëê§Œà"
helo_name <STRING>|<QUOTED-STRING>
} #SMTP_CHECK

#MISC方式，这个可以用来检查很多服务器只需要自己会些脚本即可
MISC_CHECK
{
misc_path <STRING>|<QUOTED-STRING> #外部程序或脚本
misc_timeout <INT>                                    #脚本或程序执行超时时间

misc_dynamic                                               #这个就很好用了，可以非常精确的来调整权重，是后端每天服务器的压力都能均衡调配，这个主要是通过执行的程序或脚本返回的状态代码来动态调整weight值，使权重根据真实的后端压力来适当调整，不过这需要有过硬的脚本功夫才行哦
#返回0：健康检查没问题，不修改权重
#返回1：健康检查失败，权重设置为0
#返回2-255：健康检查没问题，但是权重却要根据返回代码修改为返回码-2，例如如果程序或脚本执行后返回的代码为200，#那么权重这回被修改为 200-2
}
} # Realserver
} # Virtual Server

配置文件到此就讲完了，下面是一份未加备注的完整配置文件
global_defs
{
notification_email
{
admin@example.com
}
notification_email_from admin@example.com
smtp_server 127.0.0.1
stmp_connect_timeout 30
router_id node1
}
notification_email
{
admin@example.com
admin@ywlm.net
}

static_ipaddress
{
192.168.1.1/24 brd + dev eth0 scope global
192.168.1.2/24 brd + dev eth1 scope global
}
static_routes
{
src $SRC_IP to $DST_IP dev $SRC_DEVICE
src $SRC_IP to $DST_IP via $GW dev $SRC_DEVICE
}

vrrp_sync_group VG_1 {
group {
http
mysql
}
notify_master /path/to/to_master.sh
notify_backup /path_to/to_backup.sh
notify_fault "/path/fault.sh VG_1"
notify /path/to/notify.sh
smtp_alert
}
group {
http
mysql
}


vrrp_script check_running {
   script "/usr/local/bin/check_running"
   interval 10
   weight 10
}


vrrp_instance http {
state MASTER
interface eth0
dont_track_primary
track_interface {
eth0
eth1
}
mcast_src_ip <IPADDR>
garp_master_delay 10
virtual_router_id 51
priority 100
advert_int 1
authentication {
auth_type PASS
autp_pass 1234
}
virtual_ipaddress {
#<IPADDR>/<MASK> brd <IPADDR> dev <STRING> scope <SCOPT> label <LABEL>
192.168.200.17/24 dev eth1
192.168.200.18/24 dev eth2 label eth2:1
}
virtual_routes {
# src <IPADDR> [to] <IPADDR>/<MASK> via|gw <IPADDR> dev <STRING> scope <SCOPE> tab
src 192.168.100.1 to 192.168.109.0/24 via 192.168.200.254 dev eth1
192.168.110.0/24 via 192.168.200.254 dev eth1
192.168.111.0/24 dev eth2
192.168.112.0/24 via 192.168.100.254
}
track_script {
check_running weight 20
}

nopreempt
preemtp_delay 300
debug
}

virtual_server_group <STRING> {
# VIP port
< IPADDR> <PORT>
< IPADDR> <PORT>
fwmark <INT>
}

virtual_server 192.168.1.2 80 {
delay_loop 3

lb_algo rr|wrr|lc|wlc|lblc|sh|dh
lb_kind NAT|DR|TUN
persistence_timeout 120
persistence_granularity <NETMASK>
protocol TCP
ha_suspend
virtualhost <string>

sorry_server <IPADDR> <PORT>

real_server <IPADDR> <PORT>
{
weight 1
inhibit_on_failure 
notify_up <STRING> | <QUOTED-STRING>
notify_down <STRING> | <QUOTED-STRING>

#HTTP_GET方式
HTTP_GET | SSL_GET
{
url { 
path / 
digest <STRING>                                            
status_code 200
}
connect_port 80 

bindto <IPADD>
connect_timeout   3
nb_get_retry 3
delay_before_retry 2
} 
}
}
复制代码

Keepalived双机热备

这里我们仅仅只利用Keepalive做双机热备，也就是保证服务器的高可用性，其他的不用管。可能您会说这样在实际应用中很少会这样用，这您可就错了，Keepalived仅仅做双机热备的情况还是有的，我就碰到过几次这样的案例，下面就我碰到的几个案例做个小结

一，Keepalived双机热备的应用场景

1，网站流量不高，压力不大，但是对服务器的可靠性要求极其高，例如实时在线OA系统，政府部门网站系统，医院实时报医系统，公安局在线报案系统，股市后台网站系统等等，他们的压力不是很大，但是对可靠性要求是非常高的

2，有钱没地方花的，典型的政府企业，公办学校等等

二，Keepalived双机热备的特性以及优缺点

特性：
1，至少需要两台服务器，其中一台为master始终提供服务，另外一台作为backup始终处于空闲状态，只有在主服务器挂掉的时候他就来帮忙了，这是典型的双击热备

2，能根据需求判断服务是否可用，在不可用的时候要即使切换
优缺点：

优点：数据同步非常简单，不像负载均衡对数据一致性要求非常高，实现起来相对复杂维护也颇为不便，双机热备用rsync就可以实现了操作和维护非常简单

缺点：服务器有点浪费，始终有一台处于空闲状态


三，Keepalived双机热备的配置
首先画个双机热备拓扑图吧：




这里我只写最终实现的配置，至于Keepalived的理论知识请参考《Keepalived原理与实战精讲》

1，本例通过Keepalived来实现两台LNMP(也就是linux+nginx+mysql+php)架构服务器的双机热备

LNMP的配置请参考：《Lnmp配置精讲第一版》

2，Keepalived配置双机安装配置

1》Keepalived安装

keepalived官方地址：http://www.keepalived.org/download.html，大家可以到这里下载最新版本的keepalived

操作系统：centos 5.5 32bit
系统安装：最小化安装，也就是去掉所有组件
环境配置：安装make 和 gcc openssl openssl-devel等等
yum -y install gcc make openssl openssl-devel wget kernel-devel
mkdir -p /usr/local/src/hasoft
cd /usr/local/src/hasoft
wget http://www.keepalived.org/software/keepalived-1.2.2.tar.gz
tar -zxvf keepalived-1.2.2.tar.gz
cd keepalived-1.2.2
./configure --prefix=/usr/local/keepalived --with-kernel-dir=/usr/src/kernels/2.6.18-238.19.1.el5-i686/
复制代码
预编译后出现：
Keepalived configuration
------------------------
Keepalived version       : 1.2.2
Compiler                 : gcc
Compiler flags           : -g -O2 -DETHERTYPE_IPV6=0x86dd
Extra Lib                : -lpopt -lssl -lcrypto
Use IPVS Framework       : Yes
IPVS sync daemon support : Yes
IPVS use libnl           : No
Use VRRP Framework       : Yes
Use Debug flags          : No
复制代码
make && make install
复制代码
这里注意哦，我上面是指通用的安装方法，如果你没有用到LVS可以把lvs去掉即
./configure --prefix=/usr/local/keepalived --with-kernel-dir=/usr/src/kernels/2.6.18-238.19.1.el5-i686/ --disable-lvs-syncd --disable-lvs

但这个没有影响，就按照我的来配置吧，不过如果你要是集成了LVS，那么就不可加这两个参数了哦

整理管理文件：
cp /usr/local/keepalived/sbin/keepalived /usr/sbin/
cp /usr/local/keepalived/etc/sysconfig/keepalived /etc/sysconfig/
cp /usr/local/keepalived/etc/rc.d/init.d/keepalived /etc/init.d/


建立配置文件目录(注意：keepalived的配置文件默认在/etc/keepalived/目录)
mkdir -p /etc/etc/keepalived/

两台服务器(两个节点)都这样安装即可

2》配置

节点A配置如下：
vi /etc/keepalived/keepalived.conf
global_defs
{
notification_email
{
admin@example.com
admin@ywlm.net
}
notification_email_from admin@example.com
smtp_server 127.0.0.1
stmp_connect_timeout 30
router_id lnmp_node1
}

vrrp_instance lnmp {
state MASTER
interface eth0
virtual_router_id 100
priority 200
advert_int 5
track_interface {
eth0
eth1
}
authentication {
auth_type PASS
auth_pass 123456
}
virtual_ipaddress {
192.168.17.200
}
}
复制代码

节点B配置如下：
vi /etc/keepalived/keepalived.conf

global_defs
{
notification_email
{
admin@example.com
admin@ywlm.net
}
notification_email_from admin@example.com
smtp_server 127.0.0.1
stmp_connect_timeout 30
router_id lnmp_node1
}

vrrp_instance lnmp {
state MASTER
interface eth0
virtual_router_id 100
priority 150
advert_int 5
track_interface {
eth0
eth1
}
authentication {
auth_type PASS
auth_pass 123456
}
virtual_ipaddress {
192.168.17.200
}
}
复制代码
四，启动调试
在节点A上启动
/usr/local/keepalived/sbin/keepalived

启动日志：
Sep  8 18:26:02 centosa Keepalived_vrrp: Registering Kernel netlink reflector
Sep  8 18:26:02 centosa Keepalived_vrrp: Registering Kernel netlink command channel
Sep  8 18:26:02 centosa Keepalived_vrrp: Registering gratutious ARP shared channel
Sep  8 18:26:02 centosa Keepalived_vrrp: Opening file '/etc/keepalived/keepalived.conf'.
Sep  8 18:26:02 centosa Keepalived_vrrp: Configuration is using : 36076 Bytes
Sep  8 18:26:02 centosa Keepalived_vrrp: Using LinkWatch kernel netlink reflector...
Sep  8 18:26:02 centosa Keepalived: Starting VRRP child process, pid=5606
Sep  8 18:26:07 centosa Keepalived_vrrp: VRRP_Instance(lnmp) Transition to MASTER STATE
Sep  8 18:26:12 centosa Keepalived_vrrp: VRRP_Instance(lnmp) Entering MASTER STATE
Sep  8 18:26:12 centosa avahi-daemon[2528]: Registering new address record for 192.168.17.200 on eth0.


在节点B上启动
/usr/local/keepalived/sbin/keepalived

开机自动启动
echo /usr/local/keepalived/sbin/keepalived >> /etc/rc.local

启动日志：
Sep  8 18:30:02 centosb Keepalived: Starting Keepalived v1.2.2 (09/08,2011)
Sep  8 18:30:02 centosb Keepalived: Starting Healthcheck child process, pid=5837
Sep  8 18:30:02 centosb Keepalived_vrrp: Registering Kernel netlink reflector
Sep  8 18:30:02 centosb Keepalived_vrrp: Registering Kernel netlink command channel
Sep  8 18:30:02 centosb Keepalived_vrrp: Registering gratutious ARP shared channel
Sep  8 18:30:02 centosb Keepalived: Starting VRRP child process, pid=5839
Sep  8 18:30:02 centosb kernel: IPVS: Registered protocols (TCP, UDP, AH, ESP)
Sep  8 18:30:02 centosb kernel: IPVS: Connection hash table configured (size=4096, memory=32Kbytes)
Sep  8 18:30:02 centosb kernel: IPVS: ipvs loaded.
Sep  8 18:30:02 centosb Keepalived_healthcheckers: Registering Kernel netlink reflector
Sep  8 18:30:02 centosb Keepalived_healthcheckers: Registering Kernel netlink command channel
Sep  8 18:30:02 centosb Keepalived_healthcheckers: Opening file '/etc/keepalived/keepalived.conf'.
Sep  8 18:30:02 centosb Keepalived_vrrp: Opening file '/etc/keepalived/keepalived.conf'.
Sep  8 18:30:02 centosb Keepalived_vrrp: Configuration is using : 36252 Bytes
Sep  8 18:30:02 centosb Keepalived_vrrp: Using LinkWatch kernel netlink reflector...
Sep  8 18:30:02 centosb Keepalived_healthcheckers: Configuration is using : 6271 Bytes
Sep  8 18:30:02 centosb Keepalived_healthcheckers: Using LinkWatch kernel netlink reflector...
Sep  8 18:30:02 centosb Keepalived_vrrp: VRRP_Instance(lnmp) Entering BACKUP STATE

从日志可以看出，启动都没有问题，并且安装我给的优先级完成了竞选，各自成就了各自的状态

关闭节点A的网卡测试切换是否正常
ifdown eth0

观察节点B的日志：
Sep  8 18:32:55 centosb Keepalived_vrrp: VRRP_Instance(lnmp) Transition to MASTER STATE
Sep  8 18:33:00 centosb Keepalived_vrrp: VRRP_Instance(lnmp) Entering MASTER STATE
Sep  8 18:33:00 centosb avahi-daemon[2531]: Registering new address record for 192.168.17.200 on eth0.


启动节点A的网卡测试切换是否正常
ifup eth0
观察节点B的日志：
Sep  8 18:33:31 centosb Keepalived_vrrp: VRRP_Instance(lnmp) Received higher prio advert
Sep  8 18:33:31 centosb Keepalived_vrrp: VRRP_Instance(lnmp) Entering BACKUP STATE
Sep  8 18:33:31 centosb avahi-daemon[2531]: Withdrawing address record for 192.168.17.200 on eth0.

Received higher prio advert：表示接收到更高优先级的公告(advert公告的意思)
Withdrawing：撤回的意思，可以看出切换过程一目了然


OK，到这里我们的安装部分完成，下面我们来看看如何监控服务吧，我们这里仅仅是监控了网络故障和keepalived本身进程，在网络或者keepalived进程出现问题的时候会切换，但是我的节点A里面还有很多服务呢，例如nginx，PHP，mysql进程出问题或高负载的时候相应过慢怎么办，怎么切换的呢，这时就要用到脚本了，下面我们来看看keepalived是如何控制脚本来实现对服务器的监控和切换的

写个脚本来实时监控三个服务，若有一个出现问题遍切换mkdir /root/shell/
cd /root/shell
vi keepcheck.sh
#!/bin/bash
while  :
do
mysqlcheck=`/usr/local/lnmp/mysql/bin/mysqladmin -uroot ping 2>&1`
mysqlcode=`echo $?`
phpcheck=`ps -C php-fpm --no-header | wc -l`
nginxcheck=`ps -C nginx --no-header | wc -l`
keepalivedcheck=`ps -C keepalived --no-header | wc -l`
if [ $nginxcheck -eq 0 ]|| [ $phpcheck -eq 0 ]||[ $mysqlcode -ne 0 ];then
                if [ $keepalivedcheck -ne 0 ];then
                   killall -TERM keepalived
                else
                   echo "keepalived is stoped"
                fi
        else
                if [ $keepalivedcheck -eq 0 ];then
                   /etc/init.d/keepalived start
                else
                   echo "keepalived is running"
                fi
fi
sleep 5
done

复制代码
注意，用/etc/init.d/keepalived start如果起不来，可以用/usr/local/keepalived/sbin/keepalived二进制文件直接执行启动即可
启动脚本：
chmod +x /root/shell/keepcheck.sh
nohup sh /root/shell/keepcheck.sh &
复制代码
节点B也用这个脚本

写入/etc/rc.local开机自动启动

echo "nohup sh /root/shell/keepcheck.sh &" >> /etc/rc.loal
```

