```shell
FUSER
fuser功能
fuser 可以显示出当前哪个程序在使用磁盘上的某个文件、挂载点、甚至网络端口，并给出程序进程的详细信息.
fuser显示使用指定文件或者文件系统的进程ID.默认情况下每个文件名后面跟一个字母表示访问类型。
访问类型如下：
c 代表当前目录
e 将此文件作为程序的可执行对象使用
f 打开的文件。默认不显示。
F 打开的文件，用于写操作。默认不显示。
r 根目录。
m 映射文件或者共享库。

s 将此文件作为共享库（或其他可装载对象）使用
当指定的文件没有被访问，或者出现错误的时候，fuser会返回非零。
为了查看使用tcp和udp套接字的进程，需要-n选项并指定名称空间。默认IpV4和IpV6都会显示。套接字可以是本地的或者是远程的端口，和远程的地址。所有的域是可选的，但是其前面的','必须存在。如下：
[lcl_port][,[rmt_host][,[rmt_port]]]
对于ip地址和port，名称和数字表示都可以使用。
fuser只把PID输出到标准输出，其他的都输出到标准错误输出。
常用选项
-a 显示所有命令行中指定的文件，默认情况下被访问的文件才会被显示。
-c 和-m一样，用于POSIX兼容。
-k 杀掉访问文件的进程。如果没有指定-signal就会发送SIGKILL信号。
-i 杀掉进程之前询问用户，如果没有-k这个选项会被忽略。
-l 列出所有已知的信号名称。
-m name 指定一个挂载文件系统上的文件或者被挂载的块设备（名称name）。这样所有访问这个文件或者文件系统的进程都会被列出来。如果指定的是一个目录会自动转换成"name/",并使用所有挂载在那个目录下面的文件系统。
-n space 指定一个不同的命名空间(space).这里支持不同的空间文件(文件名，此处默认)、tcp(本地tcp端口)、udp(本地udp端口)。对于端口， 可以指定端口号或者名称，如果不会引起歧义那么可以使用简单表示的形式，例如：name/space (即形如:80/tcp之类的表示)。
-s 静默模式，这时候-u,-v会被忽略。-a不能和-s一起使用。
-signal 使用指定的信号，而不是用SIGKILL来杀掉进程。可以通过名称或者号码来表示信号(例如-HUP,-1),这个选项要和-k一起使用，否则会被忽略。
-u 在每个PID后面添加进程拥有者的用户名称。
-v 详细模式。输出似ps命令的输出，包含PID,USER,COMMAND等许多域,如果是内核访问的那么PID为kernel. -V 输出版本号。
-4 使用IPV4套接字,不能和-6一起应用，只在-n的tcp和udp的命名存在时不被忽略。
-6 使用IPV6套接字,不能和-4一起应用，只在-n的tcp和udp的命名存在时不被忽略。
- 重置所有的选项，把信号设置为SIGKILL. 

使用示例

显示使用某个文件的进程信息
$ fuser -um /dev/sda2
/dev/sda2: 6378c(quietheart) 6534c(quietheart) 6628(quietheart)
6653c(quietheart) 7429c(quietheart) 7549c(quietheart) 7608c(quietheart)
这个命令在umount的时候很有用，可以找到还有哪些用到这个设备了。
杀掉打开readme文件的程序

$fuser -m -k -i readme
这里，会在kill之前询问是否确定。最好加上-v以便知道将要杀那个进程。
查看那些程序使用tcp的80端口
$fuser -v -n tcp 80
或

$fuser -v 80/tcp
fuser不同信号的应用
用 -l参数可以列出fuser所知的信号
# fuser -l
HUP INT QUIT ILL TRAP ABRT IOT BUS FPE KILL USR1 SEGV USR2 PIPE ALRM TERM
STKFLT CHLD CONT STOP TSTP TTIN TTOU URG XCPU XFSZ VTALRM PROF WINCH IO PWR SYS
UNUSED
fuser可以发送它已知的信号给访问的指定文件进程而代替-k参数默认发送的SIGKILL，例如：只是挂起进程，那么发送HUP信号就可以了
# fuser -v /root/install.log
用户 进程号 权限 命令
/root/install.log: root 3347 f.... tail
# fuser -k -SIGHUP /root/install.log
/root/install.log: 3347
# fuser -v /root/install.log



一，为什么要使用fuser?
   先说 fuser的作用，
   fuser能识别出正在对某个文件或端口访问的进程
   大家想一下，还有哪个命令具备这个功能?
   没错，是lsof,
   我们前面讲过, lsof能够找出正在对指定文件访问的进程，
   那么它们两者之间有何区别?
   fuser有一个特别的用法在于它可以一次杀死那些正在访问指定文件的进程
   

二，如何使用fuser?
 
   1,如何用fuser得到正在使用指定文件的进程?
     用法: fuser 文件
     说明：它会把正在使用当前文件的进程id列出

     [root@localhost lhd]# umount /
     umount: /: device is busy.
           (In some cases useful info about processes that use
            the device is found by lsof(8) or fuser(1))
     [root@localhost lhd]# fuser /
      /:                       1rc     2rc     3rc     4rc     5rc     6rc     7rc    80rc    82rc    84rc    85rc   153rc   157rc   158rc
                               160rc   165rc   168rc  203rc   204rc   205rc   253rc   441rc   444rc   516rc   521rc   524rc   582rc   583rc
                               584rc   633rc  1052rc  1392rc  1394rc  1417rc  1597rc  1609rc  1617rc  1620rc  1683rc  1744rc  1783r  1785rc
                               1788rc  1806r  1808r  1810rc  1811rc  1812rc  1813rc  1814rc  1815rc  1848rc  1886rc  1899rc  1900rc  2001rc
                               ......太多不一一列出

      说明:
      这些进程号后面的rc是什么意思?
     
      c 将此文件作为当前目录使用。
      e 将此文件作为程序的可执行对象使用。
      r 将此文件作为根目录使用。
      s 将此文件作为共享库（或其他可装载对象）使用

   2,如何列出进程的详细信息，而不仅仅是进程id?
     用 -v参数即可
     说明: -v:  含义是:verbose output,详细的输出信息
     例子:

     [root@dev ~]# fuser /var/log
     /var/log:             4196c
     [root@dev ~]# fuser -v /var/log
     
                          USER        PID ACCESS COMMAND
     /var/log:            root       4196 ..c.. bash

    3,如何列出进程所属的用户?
     用 -u参数即可
     说明: -u: 含义：display user IDs，显示用户id
    
     例子:
     [root@dev ~]# fuser -u /var/log
     /var/log:             4196c(root)
     说明: -n: 含义：获得正在访问某一端口的进程号，然后可以用kill命令杀死。

     [root@dev ~]# fuser -un tcp 25
      25/tcp:             4196(root)
     4,如何杀死所有正在访问指定文件的进程?
     用 -k参数即可
     说明: -k：含义: kill processes accessing the named file

     例子:

     [root@localhost lhd]# fuser -v /root/install.log
                          用户     进程号 权限   命令
     /root/install.log:   root       3185 f.... tail
     [root@localhost lhd]# fuser -k /root/install.log
     /root/install.log:    3185
     [root@localhost lhd]# fuser -v /root/install.log

     说明: -k参数能够杀死所有的正在访问指定文件的进程，所以用来杀进程时非常方便
     说明之二: fuser如何杀死的进程？
             它发送的是这个信号:SIGKILL



三，多学一点知识

    1,fuser可以列出它所知的信号:
     用 -l参数即可
    
     例子:
     [root@dev ~]# fuser -l
     HUP INT QUIT ILL TRAP ABRT IOT BUS FPE KILL USR1 SEGV USR2 PIPE ALRM TERM
     STKFLT CHLD CONT STOP TSTP TTIN TTOU URG XCPU XFSZ VTALRM PROF WINCH IO PWR SYS
     UNUSED

    2,fuser可以发送它已知的信号给访问的指定文件进程而代替-k参数默认发送的SIGKILL
      例如：只是挂起进程，那么发送HUP信号就可以了
    
      例子:
      [root@localhost lhd]# fuser -v /root/install.log
                           用户     进程号 权限   命令
      /root/install.log:   root       3347 f.... tail
      [root@localhost lhd]# fuser -k -SIGHUP /root/install.log
      /root/install.log:    3347
      [root@localhost lhd]# fuser -v /root/install.log

LSOF
lsof 拥有更多的功能
# lsof -i 看系统中有哪些开放的端口，哪些进程、用户在使用它们，比 netstat -lptu 的输出详细。

# lsof -i 4  查看IPv4类型的进程
COMMAND    PID        USER   FD   TYPE DEVICE SIZE NODE NAME
exim4     2213 Debian-exim    4u  IPv4   4844       TCP *:smtp (LISTEN)
dhclient3 2306        root    4u  IPv4   4555       UDP *:bootpc

# lsof -i 6  查看IPv6类型的进程
COMMAND  PID        USER   FD   TYPE DEVICE SIZE NODE NAME
exim4   2213 Debian-exim    3u  IPv6   4820       TCP *:smtp (LISTEN)

# lsof -i @192.168.1.2  查看与某个具体的IP相关联的进程
COMMAND  PID USER   FD   TYPE DEVICE SIZE NODE NAME
amule   3620 root   16u  IPv4  11925       TCP 192.168.1.2:42556->77.247.178.244:4242 (ESTABLISHED)
amule   3620 root   28u  IPv4  11952       TCP 192.168.1.2:49915->118-166-47-24.dynamic.hinet.net:5140 (ESTABLISHED)

# lsof -p 5670 查看PID为5670的进程打开的文件。


==========================================================================================
lsof 与fuser
服务器上调试程序时，有时会遇到所需资源被占用的情况，这时就要查一下是什么程序占用了资源。需要用到lsof与fuser指令。

lsof用于显示打开文件的信息

lsof  filename 显示打开指定文件的所有进程
lsof -a 表示两个参数都必须满足时才显示结果
lsof -c string   显示COMMAND列中包含指定字符的进程所有打开的文件
lsof -u username  显示所属user进程打开的文件
lsof -g gid 显示归属gid的进程情况
lsof +d /DIR/ 显示目录下被进程打开的文件
lsof +D /DIR/ 同上，但是会搜索目录下的所有目录，时间相对较长l

例如

vigar@vigar-laptop:~/下载/nice$ lsof /opt/google/chrome/chrome
COMMAND  PID  USER  FD   TYPE DEVICE SIZE/OFF    NODE NAME
chrome  4913 vigar txt    REG    8,6 68449288 1835017 /opt/google/chrome/chrome
chrome  4917 vigar txt    REG    8,6 68449288 1835017 /opt/google/chrome/chrome
chrome  4919 vigar txt    REG    8,6 68449288 1835017 /opt/google/chrome/chrome

lsof查特定端口

hpcentos5-/opt/test/bin> lsof -itcp:5000
COMMAND  PID    USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
perl    8587 admin    3u  IPv4 510003      0t0  TCP hpcentos5:commplex-main (LISTEN)
 

fuser 的作用与lsof非常相似，也会将使用指定文件的进程信息全部列出

vigar@vigar-laptop:~/下载/nice$ fuser -v /opt/google/chrome/chrome
                     用户     进程号 权限   命令
/opt/google/chrome/chrome:
                     vigar      4913 ...e. chrome
                     vigar      4917 ...e. chrome
                     vigar      4919 ...e. chrome
     权限含义：

　  c 将此文件作为当前目录使用。 
      e 将此文件作为程序的可执行对象使用。 
      r 将此文件作为根目录使用。 
      s 将此文件作为共享库（或其他可装载对象）使用

 

   fuser另一个非常有用的功能是-k,可以杀死指定进程（发SIGKILL信号）  

复制代码
vigar@vigar-laptop:~/下载/nice$ fuser aaa.avi
aaa.avi:             14097
vigar@vigar-laptop:~/下载/nice$ fuser -k aaa.avi
aaa.avi:             14097
vigar@vigar-laptop:~/下载/nice$ fuser aaa.avi
vigar@vigar-laptop:~/下载/nice$ 
可知占用aaa.avi的进程已经被干掉。当然，执行这条指令需要足够的权限，有root权限最好，其它人是无法杀死别人的进程的。
复制代码
 当利用lsof或fuser查出指定进程号后,即可用netstat -anp | grep pid查出其开放端口

复制代码
test> netstat -anp | grep 7172
(Not all processes could be identified, non-owned process info
tcp        0      0 :::8777                     :::*                        LISTEN      7172/java           
tcp        0      0 ::ffff:10.0.7.128:23564     ::ffff:10.0.3.103:1521      ESTABLISHED 7172/java           
tcp        0      0 ::ffff:10.0.7.128:23550     ::ffff:10.0.3.103:1521      ESTABLISHED 7172/java           
tcp        0      0 ::ffff:10.0.7.128:10629     ::ffff:10.0.3.91:1414       ESTABLISHED 7172/java           
tcp        0      0 ::ffff:10.0.7.128:23562     ::ffff:10.0.3.103:1521      ESTABLISHED 7172/java           
tcp        0      0 ::ffff:10.0.7.128:23572     ::ffff:10.0.3.103:1521      ESTABLISHED 7172/java           
复制代码
 有时在机器上查出某个进程在跑，却不知道程序部署在何处，可以用ps -ef 查出pid,用lsof| grep pid，根据得到的信息推断出程序位置
==========================================================================================
如果要查看当前系统中打开的文件，lsof命令与fuser命令都可以实现。

1.lsof命令

lsof是LiSt Open Files的简写。
该用以给出系统中打开的文件的列表，并给出关联的进程和用户；此外还可以用以采集系统的网络连接信息。
该命令的常用参数说明：
+d d_path   只扫描给出的目录
+D d_path   递归扫描所有子目录
-i IP@host:port  扫描网络
-N  开启对NFS mount的扫描
-t  将输出结果序列号，常常用于脚本或管道中
-u UserID   扫描该用户打开的文件
2.fuser命令
fuser可以看作是轻量级的lsof。默认只给出系统中打开文件的进程PID。
该命令的常用参数说明：
 -v  给出类似lsof命令的详细信息
-ik  在kill打开某文件的进程时提示确认
```

