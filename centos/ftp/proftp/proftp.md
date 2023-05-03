```shell
为啥要使用proftpd？
主要是用它的主动模式（Port Mode) 比较好用。
有的时候 你的客户 只能 给你 开 有限的端口 用于数据传输，比如就 2个固定的端口，那么此时 主动模式 (Port Mode) 的ftp server就显得有意义：只需要开2个端口：21和20（21用于认证，20用于传输），可能 传输 效率低点(不像Passive模式，传输时随机开一个新端口），但是安全有保障，适用于严肃、珍惜网络流量（卫星网络流量）的场景。
当然，把proftpd/vsftpd的被动模式（Passive Mode)里，可以开的端口地址范围缩小，比如缩小的2个地址，那么proftpd/vsftpd的被动模式一共也就占用3个端口，那么也能达到类似效果。

proftp限制FTP模式
 以前搞过proftp禁止主动模式，但是这两天有这个需求的时候却忘记了，看来得写一日志记录下。

首先，环境是 fedora 17 + proftp 。

默认的配置是主动模式和被动模式都可用。

主动模式下访问记录：

Command:    PORT 127,0,0,1,237,87
Response:    200 PORT command successful

被动模式访问记录：

Command:    PASV
Response:    227 Entering Passive Mode (127,0,0,1,157,107).

如果需要禁止主动模式访问，则只需要在/etc/proftd.conf的<Global>段中添加如下三行：

<Limit PORT>
  DenyAll
</Limit>

即禁止PORT指令，则主动模式访问记录变为：

Command:    PORT 127,0,0,1,199,3
Response:    501 PORT: Operation not permitted

如果需要禁止被动模式访问，则只需要在/etc/proftd.conf的<Global>段中添加如下三行：

<Limit PASV>
  DenyAll
</Limit>

即禁止PASV指令，则被动模式访问记录变为：

Command:    PASV
Response:    501 PASV: Operation not permitted
-----------------------------------
©著作权归作者所有：来自51CTO博客作者RobberPhex的原创作品，如需转载，请注明出处，否则将追究法律责任
proftp限制FTP模式
https://blog.51cto.com/robberphex/1032512

proftpd安装及基本配置
proftpd安装及基本配置 - 简书 (jianshu.com)
系统：CentOS release 6.5 (Final
IP：192.168.1.93
proftpd版本：1.3.6
proftpd用户/密码：ftpadmin/ftptest
文章中的防火墙自定义
安装yum依赖包
yum -y install gcc gcc-c++ autoconf automake
下载proftpd安装包
wget ftp://ftp.proftpd.org/distrib/source/proftpd-1.3.6.tar.gz
解压安装包并编译
tar -zxf proftpd-1.3.6.tar.gz
cd proftpd-1.3.6
./configure --prefix=/usr/local/ftp && make && make install
建立FTP组和FTP用户（用户名、用户组），设置密码
mkdir /opt/ftp_soft   #创建用户的家目录
groupadd ftpgroup   
useradd ftpadmin -g ftpgroup -d /opt/ftp_soft -s /sbin/nologin  #创建并指定家目录
passwd ftpadmin
chown ftpadmin:ftpgroup /opt/ftp_soft -R     #设置属主：数组，否则即时安装成功也没有权限
修改配置文件
vim /usr/local/ftp/etc/proftpd.conf
#修改
User    ftpadmin  
Group  ftpgroup
DefaultRoot  /opt/ftp_soft
#添加
PassivePorts 11100 11111  #被动模式端口段（数据传输）
DefaultAddress          192.168.1.93 
添加防火墙
vim /etc/sysconfig/iptables
-A INPUT -p tcp --dport 11100:11111 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 21 -j ACCEPT
重新加载防火墙
/etc/sysconfig/iptables
启动proftpd服务
/usr/local/ftp/sbin/proftpd
关闭proftpd服务
killall proftpd或kill -9 PID号
脚本
#/bin/bash
FTP_HOME=/usr/local/ftp
FTP_SOFT=proftpd-1.3.6.tar.gz
FTP_IP=` ifconfig eth0 | grep "inet addr" | awk '{ print $2}'| awk -F: '{print $2}'`
cd /root
yum -y install gcc gcc-c++ autoconf automake

if [ ! -f "$FTP_SOFT" ]; then
    wget  ftp://ftp.proftpd.org/distrib/source/proftpd-1.3.6.tar.gz
fi

tar -zxf proftpd-1.3.6.tar.gz
cd /root/proftpd-1.3.6
./configure --prefix=$FTP_HOME && make && make install


#建立FTP组和FTP用户（用户名、用户组），设置密码
mkdir /opt/soft

echo "user:"
read USER
echo "set password:"
read PASSWORD
groupadd proftp
useradd $USER -g proftp -d /opt/soft -s /sbin/nologin
chown $USER:proftp /opt/soft -R

echo "$PASSWORD"| passwd --stdin $USER

#修改配置文件
sed -i '29s/^/#/g' $FTP_HOME/etc/proftpd.conf
sed -i '30s/^/#/g' $FTP_HOME/etc/proftpd.conf
sed -i '34s/^/#/g' $FTP_HOME/etc/proftpd.conf
echo "User $USER
Group proftp
DefaultRoot /opt/soft
PassivePorts 11100 11111
DefaultAddress  $FTP_IP" >>$FTP_HOME/etc/proftpd.conf #被动模式端口段（数据传输）

#添加防火墙
sed -i '/--dport 22/a\-A INPUT -m state --state NEW -m tcp -p tcp --dport 21 -j ACCEPT' /etc/sysconfig/iptables
sed -i '/--dport 22/a\-A INPUT -p tcp --dport 11100:11111 -j ACCEPT' /etc/sysconfig/iptables
#重新加载防火墙
/etc/init.d/iptables reload

#启动服务
$FTP_HOME/sbin/proftpd
FTP基础知识 FTP port(主动模式) pasv(被动模式) 及如何映射FTP

简介： 您是否正准备搭建自己的FTP网站？您知道FTP协议的工作机制吗？您知道什么是PORT方式？什么是PASV方式吗？如果您不知道，或没有完全掌握，请您坐下来，花一点点时间，细心读完这篇文章。所谓磨刀不误砍柴功，掌握这些基础知识，会令您事半功倍。否则，很可能折腾几天，最后一事无成。
FTP基础知识

FTP是File Transfer Protocol（文件传输协议）的缩写，用来在两台计算机之间互相传送文件。相比于HTTP，FTP协议要复杂得多。复杂的原因，是因为FTP协议要用到两个TCP连接，一个是命令链路，用来在FTP客户端与服务器之间传递命令；另一个是数据链路，用来上传或下载数据。

FTP协议有两种工作方式：PORT方式和PASV方式，中文意思为主动式和被动式。

PORT（主动）方式的连接过程是：客户端向服务器的FTP端口（默认是21）发送连接请求，服务器接受连接，建立一条命令链路。当需要传送数据时，客户端在命令链路上用PORT命令告诉服务器：“我打开了XXXX端口，你过来连接我”。于是服务器从20端口向客户端的XXXX端口发送连接请求，建立一条数据链路来传送数据。

PASV（被动）方式的连接过程是：客户端向服务器的FTP端口（默认是21）发送连接请求，服务器接受连接，建立一条命令链路。当需要传送数据时，服务器在命令链路上用PASV命令告诉客户端：“我打开了XXXX端口，你过来连接我”。于是客户端向服务器的XXXX端口发送连接请求，建立一条数据链路来传送数据。

从上面可以看出，两种方式的命令链路连接方法是一样的，而数据链路的建立方法就完全不同。而FTP的复杂性就在于此。

FTP服务器端的注意事项

一、FTP服务器是公网IP，用公网动态域名；或是内网IP，在网关上开端口映射或用内网专业版TrueHost

1、服务器如果安装了防火墙，请记住要在防火墙上打开FTP端口（默认是21）。

2、所有FTP服务器软件都支持PORT方式。至于PASV方式，大部分FTP服务器软件都支持。支持PASV方式的FTP服务器软件，也可以设置为只工作在PORT方式上。

3、为了PASV方式能正常工作，需要在FTP服务器软件上为PASV方式指定可用的端口范围。此外，还要在服务器的防火墙上打开这些端口。当客户端以PASV方式连接服务器的时候，服务器就会在这个端口范围里挑选一个端口出来，给客户端连接。

FTP客户端的注意事项

请注意：选择用PASV方式还是PORT方式登录FTP服务器，选择权在FTP客户端，而不是在FTP服务器。

一、客户端只有内网IP，没有公网IP

从上面的FTP基础知识可知，如果用PORT方式，因为客户端没有公网IP，FTP将无法连接客户端建立数据链路。因此，在这种情况下，客户端必须要用PASV方式，才能连接FTP服务器。大部分FTP站长发现自己的服务器有人能登录上，有人登录不上，典型的错误原因就是因为客户端没有公网IP，但用了IE作为FTP客户端来登录（IE默认使用PORT方式）。

作为FTP站长，有必要掌握FTP的基础知识，然后指导您的朋友如何正确登录您的FTP。

二、客户端有公网IP，但安装了防火墙

如果用PASV方式登录FTP服务器，因为建立数据链路的时候，是由客户端向服务器发送连接请求，没有问题。反过来，如果用PORT方式登录FTP服务器，因为建立数据链路的时候，是由服务器向客户端发送连接请求，此时连接请求会被防火墙拦截。如果要用PORT方式登录FTP服务器，请在防火墙上打开1024以上的高端端口。

四、常见的FTP客户端软件PORT方式与PASV方式的切换方法。

大部分FTP客户端默认使用PASV方式。IE默认使用PORT方式。

在大部分FTP客户端的设置里，常见到的字眼都是“PASV”或“被动模式”，极少见到“PORT”或“主动模式”等字眼。因为FTP的登录方式只有两种：PORT和PASV，取消PASV方式，就意味着使用PORT方式。

IE：
工具 -> Internet选项 -> 高级 -> “使用被动FTP”（需要IE6.0以上才支持）。

CuteFTP：
Edit -> Setting -> Connection -> Firewall -> “PASV Mode”
或
File -> Site Manager，在左边选中站点 -> Edit -> “Use PASV mode”
FlashGet：
工具 -> 选项 -> 代理服务器 -> 直接连接 -> 编辑 -> “PASV模式”

FlashFXP：
选项 -> 参数选择 -> 代理/防火墙/标识 -> “使用被动模式”
或
站点管理 -> 对应站点 -> 选项 -> “使用被动模式”
或
快速连接 -> 切换 -> “使用被动模式”
LeechFTP：
Option -> Firewall -> Do not Use

五、请尽量不要用IE作为FTP客户端

IE只是个很粗糙的FTP客户端工具。首先，IE6.0以下的版本不支持PASV方式；其次，IE在登录FTP的时候，看不到登录信息。在登录出错的时候，无法找到错误的原因。在测试自己的FTP网站的时候，强烈建议不要使用IE。


高级话题

一、为什么没有公网IP，也能使用PORT方式登录FTP？

NAT网关的工作方式是在TCP/IP数据包的包头里找局域网的源地址和源端口，替换成网关的地址和端口。对数据包里的内容，是不会改变的。而使用PORT方式登录FTP的时候，IP地址与端口信息是在数据包里面的，而不是在包头。因此，没有公网IP，使用PORT方式是无法从internet上的ftp服务器下载数据的。 

但是，极少数的NAT网关也支持PORT方式。这些NAT网关连数据包里面的内容都扫描，扫描到PORT指令后会替换PORT方式的IP和端口。在这种NAT网关下面，用PORT方式就没问题了。不过，这些网关也只扫描21端口的数据包，如果FTP服务器不是用默认的21端口，也无法使用PORT方式。

内网用端口映射方式建FTP的注意事项(原创)

根据上面的FTP原理，在只映射一个端口的情况下，只能要求来访者使用PORT方式来访问你的FTP服务器,但这样的话就限制了内网用户。
而如果映射很多个端口，就可以使用PASV方式了但还要从FTP服务器上作一些设置
 
比如，从网关上映射21端口和5000-5020端口到你的计算机21端口和5000-5020端口，在Serv-U的Local Server -> Settings -> Advanced -> PASV port range里，填入给PASV模式使用的端口范围5000-5020,然后在你建立的域--设置--高级-允许被动模式数据传输，使用IP 里面填入你的网关在公网的IP地址。

还有，用端口映射方式建FTP是看不到来访者的IP地址，只能看到网关在内网中的IP地址。(应该是这样，但也不一定，毕竟我没试过)这样就不能限制一个IP的连接数了。

```

