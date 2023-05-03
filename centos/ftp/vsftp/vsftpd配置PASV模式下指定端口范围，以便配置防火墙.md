```shell
vsftpd配置PASV模式下指定端口范围，以便配置防火墙

pam_service_name=vsftpd
userlist_enable=YES
tcp_wrappers=YES
chroot_local_user=YES
allow_writeable_chroot=YES
chroot_list_enable=NO
chroot_list_file=/etc/vsftpd/chroot_list
userlist_file=/etc/vsftpd/user_list

pasv_enable=YES
pasv_min_port=50240
pasv_max_port=60240
pasv_promiscuous=NO
pasv_address=10.150.32.1

port_enable=NO
connect_from_port_20=NO
ftp_data_port=2020
pasv_promiscuous=YES

==============================================

 什么叫做PASV mode（被动模式传送)？他是如何工作的?

FTP的连接一般是有两个连接的，一个是客户程和服务器传输命令的，另一个是数据传送的连接。FTP服务程序一般会支持两种不同的模式，一种是Port模式，一种是Passive模式(Pasv Mode),我先说说这两种不同模式连接方式的分别。

先假设客户端为C,服务端为S.

Port模式:

当客户端C向服务端S连接后，使用的是Port模式,那么客户端C会发送一条命令告诉服务端S(客户端C在本地打开了一个端口N在等着你进行数据连接),当服务端S收到这个Port命令后 就会向客户端打开的那个端口N进行连接，这种数据连接就生成了。

Pasv模式:

当客户端C向服务端S连接后，服务端S会发信息给客户端C,这个信息是(服务端S在本地打开了一个端口M,你现在去连接我吧),当客户端C收到这个信息后，就可以向服务端S的M端口进行连接,连接成功后，数据连接也建立了。

从上面的解释中，大家可以看到两种模式主要的不同是数据连接建立的不同，对于Port模式，是客户端C在本地打开一个端口等服务端S去连接建立数据连接；而Pasv模式就是服务端S打开一个端口等待客户端C去建立一个数据连接。

我需要把传送模式设置成PASV mode吗?

如果您连接到INTERNET的方式如下图,你需要设置成为PASV mode模式传送

路由上有两个ip一个是内网ip（也就是你的网关地址)另外一个是外网IP,你所有LAN中的计算机都是共享这个218.63.1.5访问INTERNET的（中间通过了192.168.1.1这个网关),让我们来看一个连接的详细过程,以便您

更进一步了解为什么需要设置成为PASV mode 传送:

当您通过下载工具或使用浏览器下载影片的时候就开始连接FTP服务器, 当FTP服务收到你的连接请求后发送应答信息给客户并开始等待用户的认证信息,–>认证通过后开始建立数据连接如果你不是设置成为pasv模式,那 么客户段(下载工具)会在本地计算机开一个数据端口,然后发送一条“命令”给FTP服务段,(我已打开了n端口你现在可以连接我),那么FTP服务器段就 开始连接你ip地址的N端口,（但是连接是失败的) 因为你是通过192.168.1.1 这个网关(你的路由设备)访问internet的ftp服务段他实际上是同你的外网地址为218.63.1.5的路由设备连接,根本就连接不上你下载工具 中打开的端口,例如你使用的是 192.168.1.2 这个客户机，FTP服务段此时无法同你计算机192.168.1.2)监听端口N连接上,所以此时你会看到服务器反馈的错误信息,提示你 192.168.1.2打开的端口无法连接之类的, 信息此时你就需要把传送模式设置成为PASV模式,而如果你使用的是PASV模式那么当你ftp通过认证后,客户段首先向服务器发送一条PASV命令,服 务段接受到命令后打开一个端口并告诉客户段“我已打开了一个端口,你此时可以来连接了” 客户段（下载工具）接受到信息后,就去连接服务段已经打开了的端口,从而完成数据连接,所有ftp下载的数据流都将通过这个端口传送

而且在用iptables做NAT时，应该装载这两个模块(ip_nat_ftp、ip_conntrack_ftp)，才能正常连到ftp服务器。否则pasv被动模式都无法连接到ftp服务器。




FTP模式与数据端口

　　FTP 分为两类，PORT FTP和PASV FTP，PORT FTP是一般形式的FTP。这两种FTP在建立控制连接时操作是一样的，都是由客户端首先和FTP服务器的控制端口(默认值为21)建立控制链接，并通过 此链接进行传输操作指令。它们的区别在于使用数据传输端口(ftp- data)的方式。PORT FTP由FTP服务器指定数据传输所使用的端口，默认值为20。PASV FTP由FTP客户端决定数据传输的端口。 PASV FTP这种做法，主要是考虑到存在防火墙的环境下，由客户端与服务器进行沟通(客户端向服务器发出数据传输请求中包含了数据传输端口)，决定两者之间的数 据传输端口更为方便一些。

　　port_enable=YES|NO

　　如果你要在数据连接时取消PORT模式时，设此选项为NO。默认值为YES。

　　connetc_from_port_20=YES|NO

　　控制以PORT模式进行数据传输时是否使用20端口(ftp-data)。YES使用，NO不使用。默认值为NO，但RHL自带的vsftpd.conf文件中此参数设为YES。

　　ftp_data_port=port number

　　设定ftp数据传输端口(ftp-data)值。默认值为20。此参数用于PORT FTP模式。

　　port_promiscuous=YES|NO

　　默认值为NO。为YES时，取消PORT安全检查。该检查确保外出的数据只能连接到客户端上。小心打开此选项。


       pasv_enable=YES|NO

　　YES，允许数据传输时使用PASV模式。NO，不允许使用PASV模式。默认值为YES。

　　pasv_min_port=port number 
　　pasv_max_port=port number

　　设定在PASV模式下，建立数据传输所可以使用port范围的下界和上界，0 表示任意。默认值为0。把端口范围设在比较高的一段范围内，比如50000-60000，将有助于安全性的提高。

　　pasv_promiscuous=YES|NO

　　此选项激活时，将关闭PASV模式的安全检查。该检查确保数据连接和控制连接是来自同一个IP地址。小心打开此选项。此选项唯一合理的用法是存在于由安全隧道方案构成的组织中。默认值为NO。

　　pasv_address=

　　此选项为一个数字IP地址，作为PASV命令的响应。默认值为none，即地址是从呼入的连接套接字(incoming connectd socket)中获取。

所以如果linux打开了防火场，必须开通对应的端口 例如：

-A RH-Firewall-1-INPUT -m state –state NEW -p tcp -m tcp –dport 30000:31000 –syn -j ACCEPT


四、防火墙配置

#!/bin/bash

#ip.sh

echo “1″ >/proc/sys/net/ipv4/ip_forward

modprobe ip_conntrack_ftp

modprobe ip_nat_ftp

iptables -F

iptables -X

iptables -Z

iptables -t nat -F

iptables -t nat -X

iptables -t nat -Z

iptables -P INPUT ACCEPT

iptables -P OUTPUT ACCEPT

iptables -P FORWARD DROP

iptables -t filter -A FORWARD -p tcp -s 172.16.0.0/16 -d 192.168.0.10 –dport 21 -j ACCEPT

iptables -t filter -A FORWARD -p tcp -s 192.168.0.10 –sport 21 -j ACCEPT

iptables -t filter -A FORWARD -p tcp -s 172.16.0.0/16 -d 192.168.0.10 –dport 30000:310000 -j ACCEPT

iptables -t filter -A FORWARD -p tcp -s 192.168.0.10 –sport 3000:4000 -j ACCEPT

iptables -t filter -A FORWARD -p tcp -m state –state RELATED,ESTABLISHED -j ACCEPT

iptables -t filter -A FORWARD -p icmp –icmp-type 8 -j ACCEPT

iptables -t filter -A FORWARD -p icmp –icmp-type 0 -j ACCEPT



五、验证：

# 在xp下用ftp命令连接，都是采用主动模式连接，可以采用图形界面的软件，默认一般为被动模式。


============================================================
Linux下VsFTP配置全方案

纲要：

一， VsFTPd的简述

二， 企业Linux下vsFTPD方案

三， VsFTPD相关配置文件详解

一,VsFTPd的简述:

VSFTP（Very Secure FTP Daemon，非常安全的FTP服务器）。顾名思义，VSFTPD设计的出发点就是安全性。同时随着版本的不断升级，VSFTPD在性能和稳定性上也取得 了极大的进展。除了安全和性能方面很优秀的外，还有很好的易用性。Red Hat公司在自己的FTP服务器(ftp.redhat.com)上就使用了vsFTPd

二，企业Linux下vsFTPD实战方案

要求：

1. 用户:netseek_com；FTP：ftp.netseek.com(192.168.0.21,我已经配置好了相关的DNS)

2. 禁止匿名登陆，禁止用户离开用户目录

3. 命令通道端口使用5021，数据通道端口使用5020（你可以改成你想设置的端口如1021,1020）

4. 允许使用被动模式使用端口范围为10010~10020.

5. 用户带宽设置为200kps；用户空间大小限制为300M，允许宽限期限内有10MB的缓冲

具体操作步骤如下：

为了更完全的描述安装全过程，我在我的另一台主机（netseek）上进行配置，在台机IP:192.168.0.20上绑定了192.168.0.21,也配置好了相关的DNS解析，在这里我就不多说了请参照”Linux全能web服务器架设”。

1． 配置相关服务

启动服务：

[root@netseek root]# #rpm –qa | grep vsftpd ；查看是否安装vsftpd服务

#/etc/init.d/vsftpd start          ;启动服务

#chkconfig vsftpd on              ;让系统服务随着系统启动而启动

配置相关文档：

#vi /etc/vsftpd/vsftpd.conf

禁止匿名用户访问：

anonymous_enable=YES,将YES改为NO

在文件末尾加如下的设置：

# new added by netseek

listen_port=5021                      ;命令通道端口，默认为21

listen_data_port=5020                   ;数据通道端口，默认为20

pasv_enable=YES                      ;允许被动模式

pasv_min_port=10000                   ；被动模式使用端口范围

pasv_max_port=10010

local_max_rate=200000                    ；用户宽带限制

chroot_local_user=YES                    ；禁用户离开主目录

listen_address=192.168.0.21             ；让他监听ip:192.168.0.21

注：在添加过程中请不要加上”;”和这些中文注释，在这里我上为了方便大家了解才加上的。

2． 添加虚拟主机用户

添加用户netseek_com,用户目录指定为/var/www/netseek.com,且此用户不能登陆系统.

#mkdir /var/www/netseek.com

#useradd –s /sbin/nologin –d /var/www/netseek.com netseek_com

注-s /sbin/nologin是让其不能登陆系统，-d 是指定用户目录为/var/www/netseek.com

#passwd netseek_com           (Do not Hack me 3ks) DnHm3ks 这是我设置的密码

#chown –R netseek_com:netseek_com /var/www/netseek.com

注:将用户目录及其子目录的所有和所属的组设置为netseek_com

3． 配置磁盘配额

我在安装过程中磁盘了多个区，分别/home,/var,/，/www,tmp,swap,/boot多个分区。建议你将磁盘分多个独单的分区,这样可以降低磁盘分区损坏造成的数据丢失风险.

#[root@netseek root]# rpm -qa | grep quota

quota-3.06-9             ; 我的系统已经安装了磁盘配额工具

#vi /etc/fstab

找到类似

LABEL=/var              /var                    ext3 defaults        1 2    

在defaults后加入usrquota，改为如下：

LABEL=/var              /var                    ext3 defaults,usrquota        1 2

重新加载/var这个分区：

#mount –o remount /var

#cd /var   进入这个目录

#touch quota.user

#chmod 600 quota.user

#quotacheck –mf /var     对磁盘分区空间使用情况进行强制检测

[root@localhost var]# quotacheck -mf /var

quotacheck: WARNING – Quotafile /var/quota.user was probably truncated. Can’t save quota   settings…

重新启动系统

第一次运行时都会出现这个警告，再次运行的时候就不会啦

为指定用户定额：

#edquota netseek_com 为用户netseek_com设置磁盘配额。

Disk quotas for user netseek_com (uid 501):

   Filesystem                blocks    soft    hard     inodes     soft     hard

   /dev/sda5                   6616     307200       0       15        0        0

注：

ilesystem blocks soft hard inodes soft hard

filesystem-正在设置的文件系统，不要修改或删除

bolock-当前使用的碰盘空间，单位为KB

soft(第一个)-软磁盘空间限制，默认0，表示无限制，我在这里设为300M。

hard(第一个)-硬配额限制，0表示无限制。

inodes-当前文件数量

soft(第二个)-对文件数量的软限制,0表示无限制

hard(第二个)-对文件数量的硬限制,0表示无限制

让系统启动是自动加载:

#vi /etc/rc.d/rc.local ,加入以下:

/etc/init.d/vsftpd start

/sbin/quotacheck

/sbin/quotaon –avu

现在查看netseek_com 用户使用磁盘空间的情况

[root@localhost ~]# quota -u netseek_com

Disk quotas for user netseek_com (uid 501):

    Filesystem   blocks quota limit grace files quota limit grace

   /dev/sda5 6616   307200    0              15    0    0

注：关于更多的quota命令，请用man命令查看吧，乌哥(VBird)说，我们要学会找男人 !

4． 效果演示,登陆演示：

四， VsFTPd相关的配置详解

1.匿名用户相关设置

anonymous_enable=YES              ,将YES改为NO, 禁止匿名用户登陆

#non_mkdir_write_enable=YES        ,将#注释去掉，允许匿名用户创建目录

#non_upload_enalbe=YES          ,将#去掉，允许匿名用户上传

anon_world_readable_only=YES    ,允许匿名用户下载，默认是禁止的，这个可以自行添加。

Anon_other_write_enable=YES        ,将其设为YES的话，就除了上传和创建目录外，还可以重命名，删除文件，默认是NO

no_anon_password=NO             ,将其设为YES,匿名用户不会查询用户密码直接登陆。

ftp_username=ftp                    ,匿名用户登陆系统的账号默认为ftp,此项最好不要改，否则设置不当会给系统的安全带来威胁。

2.FTP服务端口的指定

listen_port=8021                   ,指定命令通道为8021,默认为21

listen_data_port=8020             ,指定数据通道为8020,默认为20

3.上传模式的设置

pasv_enable=YES                    ,是否允使用被动模式，默认是允许的。

pasv_min_port=10000                 ，指定使用被动模式时打开端口的最小值

pasv_max_port=10004             ，指定使用被动模式时打开端口的最大值。

4.Vsftp服务器指定IP地址

listen_address=192.168.0.21           ，指定FTP，IP地址

注：只有当vsftp运行于独立模式时才允许使用指定IP,如果在/etc/xinetd.d目录下已经建立了vsfpd文件，就要将该文件中的disable设置为yes，方可。

5. 锁定用户，禁止用户离开用户主目录

chroot_local_user=YES             ,将其设为YES，就锁定在用户主目录，设为NO，可以切换

将指定用户设置为锁定用户主目录:

#chroot_list_enable=YES

#chroot_list_file=/etc/vsftpd.chroot_list

将其改为如下:

chroot_list_enable=NO

chroot_list_file=/etc/vsftpd/vsftpd.chroot_list

将上面保存，再做如下操作：

#touch /etc/vsftpd/vsftpd.chroot_list

#vi /etc/vsftpd/vsftpd.chroot_list ,在该文件中加入用户名单，如：

netseek_com

6.FTP服务器的流量控制

max_clients=100          ;允许的最大连接数，定义为100，默认为0，表没有限制

max_per_ip=5             ;每个IP允许的连接数，0表没有限制，需要运行于独立模式方可

anon_max_rate=50000        ;匿名用户最大带宽，单位为bps

local_max_rate=200000    ;系统用户最大带宽

如何对指定用户进行流量限制呢？

#vi /etc/vsftpd/vsftpd.conf,添加一行：

user_config_dir=/etc/vsftpd/userconf

#touch /etc/vsftpd/userconf/netseek_com   为netseek_com这个用户建立一个netseek_com文件

#vi /etc/vsftpd/userconf/netseek_com   添加以下内容

local_max_rate=100000         

保存重启服务即可.

7.定制欢迎信息

   目录说明设置

   #vi /etc/vsftpd/vsftpd.conf

#dirmessage_enable=YES,前的#去掉。

然后我们定制一个.message,写上你想写的东西，然后把这个文件复制到各个用户的家目录中,就OK。

系统欢迎消息设置

ftpd_banner=Welcome to ftp.netseek.com , Yeah!!!

系统欢迎消息文件设置

banner_file=/etc/vsftpd/welcome 与ftpd_banner相类似，不同之处在于，banner_file指定欢迎文件.

```

