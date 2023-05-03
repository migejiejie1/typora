```shell
ip是iproute2软件包里面的一个强大的网络配置工具，本文将分章节介绍ip命令及其选项。为了便于理解，作者在本文中列举了很多示例。但是，正如作者所说，这不是一个教程，而是一个使用手册。

ip命令的用法如下：

ip [OPTIONS] OBJECT [COMMAND [ARGUMENTS]]

其中，OPTIONS是一些修改ip行为或者改变其输出的选项。所有的选项都是以-字符开头，分为长、短两种形式。目前，ip支持如下选项：

-V,-Version 打印ip的版本并退出。

-s,-stats,-statistics 输出更为详尽的信息。如果这个选项出现两次或者多次，输出的信息将更为详尽。

-f,-family 这个选项后面接协议种类，包括：inet、inet6或者link，强调使用的协议种类。如果没有足够的信息告诉ip使用的协议种类，ip就会使用默认值inet或者any。link比较特殊，它表示不涉及任何网络协议。

-4 是-family inet的简写。

-6 是-family inet6的简写。

-0 是-family link的简写。

-o,-oneline 对每行记录都使用单行输出，回行用字符代替。如果你需要使用wc、grep等工具处理ip的输出，会用到这个选项。

-r,-resolve 查询域名解析系统，用获得的主机名代替主机IP地址。

OBJECT是你要管理或者获取信息的对象。目前ip认识的对象包括：

link 网络设备

address 一个设备的协议（IP或者IPV6）地址

neighbour ARP或者NDISC缓冲区条目

route 路由表条目

rule 路由策略数据库中的规则

maddress 多播地址

mroute 多播路由缓冲区条目

tunnel IP上的通道

另外，所有的对象名都可以简写，例如：address可以简写为addr，甚至是a。

COMMAND设置针对指定对象执行的操作，它和对象的类型有关。一般情况下，ip支持对象的增加(add)、删除(delete)和展示(show或者list)。有些对象不支持所有这些操作，或者有其它的一些命令。对于所有的对象，用户可以使用help命令获得帮助。这个命令会列出这个对象支持的命令和参数的语法。如果没有指定对象的操作命令，ip会使用默认的命令。一般情况下，默认命令是list，如果对象不能列出，就会执行help命令。

ARGUMENTS是命令的一些参数，它们倚赖于对象和命令。ip支持两种类型的参数：flag和parameter。flag由一个关键词组成；parameter由一个关键词加一个数值组成。为了方便，每个命令都有一个可以忽略的默认参数。例如，参数dev是ip link命令的默认参数，因此ip link ls eth0等于ip link ls dev eth0。我们将在后面的章节详细介绍每个命令的使用，命令的默认参数将使用default标出。

几乎所有的关键词都可以简写为前几个字母。在交互工作时，简写的方式非常方便，但是我们不建议在脚本中使用简写形式。另外，在讲述过程中，所有的"官方"简写方式都会在文章中列出。


[root@linux ~]# ip link set dev eth0 up      #up/down 起动／关闭设备

[root@linux ~]# ip link set dev eth0 txqueuelen 100      #改变设备传输队列的长度

[root@linux ~]# ip link set dev eth0 mtu 1500      #改变网络设备MTU(最大传输单元)的值

[root@linux ~]# ip link set dev eth0 address 00:01:4f:00:15:f1      #修改网络设备的MAC地址

[root@linux ~]# ip -s -s link ls eth0      #查看eth0网卡信息 等同于ifconfig eth0

[root@linux ~]# ip addr add local 192.168.4.1/28 brd + label eth0:1 dev eth0      #为每个地址设置一个字符串作为标签

[root@linux ~]# ip addr add 192.168.4.2/24 brd + dev eth1 label eth1:1      #在以太网接口eth0上增加一个地址192.168.4.2，掩码长度为24位(155.155.155.0)，标准广播地址，标签为eth0:Alias

[root@linux ~]# ip addr del 192.168.4.1/24 brd + dev eth0 label eth0:1      #ip address delete--删除一个协议地址. 缩写：delete、del、d

[root@linux ~]# ip addr ls eth0      #ip address show--显示协议地址. 缩写：show、list、lst、sh、ls、l

[root@linux ~]# ip -s -s a f to 10/8      #删除属于私网10.0.0.0/8的所有地址

[root@linux ~]# ip -4 addr flush label "eth0"      #取消所有以太网卡的IP地址

[root@linux ~]# ip neigh add 10.0.0.3 lladdr 0:0:0:0:0:1 dev eth0 nud perm      #在设备eth0上，为地址10.0.0.3添加一个permanent ARP条目

[root@linux ~]# ip neigh chg 10.0.0.3 dev eth0 nud reachable      #把状态改为reachable

[root@linux ~]# ip neigh del 10.0.0.3 dev eth0      #删除设备eth0上的一个ARP条目10.0.0.3
```

