```shell
在Debian系统中有很多的配置文件，这些配置文件都很重要，控制着系统和应用程序的运行。下面记录这些配置文件的存放位置、作用和配置参数，以便我们在系统维护中能快速定位和配置这些文件。
1. .bash_profile、.bashrc、.bash_history和.bash_logout
上面这三个文件是bash shell的用户环境配置文件，位于用户的主目录下。其中.bash_profile是最重要的一个配置文件，它在用户每次登录系统时被读取，里面的所有 命令都会被bash执行。.profile(由Bourne Shell和Korn Shell使用)和.login(由C Shell使用)两个文件是.bash_profile的同义词，目的是为了兼容其它Shell。在Debian中使用.profile文件代替. bash_profile文件。
.bashrc文件会在bash shell调用另一个bash shell时读取，也就是在shell中再键入bash命令启动一个新shell时就会去读该文件。这样可有效分离登录和子shell所需的环境。但一般 来说都会在.bash_profile里调用.bashrc脚本，以便统一配置用户环境。
.bash_history是bash shell的历史记录文件，里面记录了你在bash shell中输入的所有命令。可通过HISTSIZE环境变量设置在历史记录文件里保存记录的条数。
.bash_logout在退出shell时被读取。所以我们可把一些清理工作的命令放到这文件中。
在/etc目录的bash.bashrc和profile是系统的配置文件，当在用户主目录下找不到.bash_profile和.bashrc时，就会读取这两个文件。
2. /etc/passwd、/etc/shadow和/etc/group
这三个配置文件用于系统帐号管理，都是文本文件，可用vi等文本编辑器打开。/etc/passwd用于存放用户帐号信息，/etc/shadow用于存放每个用户加密的密码，/etc/group用于存放用户的组信息。
/etc/passwd文件的内容如下：
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/bin/sh
bin:x:2:2:bin:/bin:/bin/sh
sys:x:3:3:sys:/dev:/bin/sh
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/bin/sh
...
每一行是由分号分隔的字串组成，它的格式如下：
username:password:uid:gid:gecos:homedir:shell
如果启用shadow口令功能，password的内容为“x”，加密的密码转而存放到/etc/shadow文件中。如果password的内容为“*”，则该帐号被停用。使用passwd这个程序可修改用户的密码。
/etc/shadow存放加密的口令，该文件只能由root读取和修改。下面是shadow文件的内容：
root:$1$43ZR5j08$kuduq1uH36ihQuiqUGi/E9:12973:0:99999:7:::
daemon:*:12973:0:99999:7:::
bin:*:12973:0:99999:7:::
sys:*:12973:0:99999:7:::
sync:*:12973:0:99999:7:::
...

我们可用chage命令显示test用户的帐号信息：
debian:~# chage -l test
最小： 0
最大： 99999
警告日： 7
失效日： -1
最后修改： 7月 09, 2005
密码过期： 从不
密码失效： 从不
帐户过期： 从不
/etc/shadow文件的格式如下：
username:password:last_change:min_change:max_change:warm:failed_expire:expiration:reserved
各字段的简要说明：
last_change：表示自从Linux使用以来，口令被修改的天数。可用chage -d命令修改。
min_change：表示口令的最小修改间隔。可用chage -m命令修改。
max_change：表示口令更改周期。可用chage -M命令修改。
warm：表示口令失效的天数。可用chage -W命令修改。
failed_expire：表示口令失效后帐号的锁定天数。可用chage -I命令修改。
expiration：表示帐号到期日时间。可用chage -E命令修改。
reserved：没有使用，留待以后使用。
在debian系统中，使用shadowconfig on/off命令可控制启用和禁用shadow口令功能。
/etc/group是帐号分组文件，控制用户如何分组。下面是组文件的内容：
root:x:0:
daemon:x:1:
bin:x:2:
sys:x:3:
adm:x:4:
...
它的格式如下：
groupname:password:gid:members
这里的password代表组口令，很少用到。它可使原先不在这个群组中的用户可以通 过newgrp 命令暂时继承该组的权限，使用newgrp命令时会新开一个shell。口令的加密方式和passwd文件中的口令一样，所以如果需设置组口令，要用 passwd程序虚设一个用户，再把该用户password节中的加密口令拷贝到/etc/group文件中。members列代表组成员，我们可把需加 入该组的用户以逗号分隔添加到这里即可。同一组的成员可继承该组所拥有的权限。
3. /etc/login.defs
login.defs是设置用户帐号限制的文件，在这里我们可配置密码的最大过期天 数，密码的最大长 度约束等内容。该文件里的配置对root用户无效。如果/etc/shadow文件里有相同的选项，则以/etc/shadow里的设置为准，也就是说 /etc/shadow的配置优先级高于/etc/login.defs。下面内容是该文件的节选：
...
#
# Password aging controls:
#
# PASS_MAX_DAYS Maximum number of days a password may be used.
# PASS_MIN_DAYS Minimum number of days allowed between password change.
# PASS_WARN_AGE Number of days warning given before a password expires.
#
PASS_MAX_DAYS 99999
PASS_MIN_DAYS 0
PASS_WARN_AGE 7
...
#
# Number of significant characters in the password for crypt().
# Default is 8, don't change unless your crypt() is better.
# If using MD5 in your PAM configuration, set this higher.
#
PASS_MAX_LEN 8
...
4. /etc/securetty
该文件可控制根用户登录的设备，该文件里记录的是可以作为根用户登录的设备名，如tty1、tty2等。用户是不能从不存在于该文件里的设备登录为根用户的。这种情况用户只能以普通用户登录进来，再用su命令转为根用户。/etc/securetty文件的格式如下：
# /etc/securetty: list of terminals on which root is allowed to login.
# See securetty(5) and login(1).
console

# for people with serial port consoles
ttyS0

# for devfs
tts/0

# Standard consoles
tty1
tty2
tty3
...
如果/etc/securetty是一个空文件，则根用户就不能从任务的设备登录系 统。只能以普通用 户登录，再用su命令转成根用户。如果/etc/securetty文件不存在，那么根用户可以从任何地方登录。这样会引发安全问题，所以 /etc/securetty文件在系统中是一定要存在的。
5. ~/.gnomerc
作用：GNOME桌面系统的用户级启动文件，该文件里的脚本在GNOME桌面系统启动 时会自动执行， 如果在用户主目录中没有该文件，用户可自行创建。该脚本是由GNOME系统级启动文件/etc/X11/Xsession.d/55gnome- session_gnomerc所触发的。在我的的系统中，该配置文件的内容如下：
# 配置GTK+程序的打开文件窗口字体编码为GBK
export G_FILENAME_ENCODING=GBK
#下面设置fcitx输入法的环境变量
export XIM_PROGRAM=fcitx
export XIM=fcitx
export XMODIFIERS="@im=fcitx"
#启动fcitx中文输入法
fcitx&
G_FILENAME_ENCODING参数的官方解析可参考网址：http://developer.gnome.org/doc/API/2.0/glib/glib-running.html
6. ~/.gtkrc.zh_CN
作用：设置GTK+ 1.x程序的配置文件，默认已有字体配置选项，与上面的~/.gnomerc配置文件中的配置GTK+程序打开文件窗口的编码选项配合使用，可使GTK+ 1.x程序能在打开文件窗口显示中文的文件名。配置文件内容如下 ：
style "gtk-default-zh-cn" {
fontset = "-adobe-helvetica-medium-r-normal--12-*-*-*-*-*-iso8859-*,
-*-*-medium-r-normal--14-*-*-*-*-*-gb2312.1980-0"
}
class "GtkWidget" style "gtk-default-zh-cn"
该文件的全局配置文件是/etc/gtk/gtkrc。如果需统一设置所有用户的gtk中文设置，可在该文件中配置。文件的内容和上面的一样。
7. ~/.gtkrc-2.0
作用：gtk2.0程序的设置文件，如果不存在，可手工创建。配置GTK2.0程序字体的配置如下：
style "gtk-default-zh-cn" {
font_name = "Bitstream Vera Sans 10,SimSun 10"
}
class "GtkWidget" style "gtk-default-zh-cn"
该文件也有一个全局配置文件/etc/gtk-2.0/gtkrc，注意是 gtkrc，而不是 gtkrc-2.0，默认该文件也是没有的，需手工创建。一旦存在~/.gtkrc-2.0或/etc/gtk-2.0/gtkrc文件，则该文件的配置 优先级是最高的，即使用gnome-font-properties字体配置程序也不能改变。例如你在~/.gtkrc-2.0里设置了字体是 SimSun 10号字，则你不能用gnome-font-properties字体配置程序更改该设置。
8. /etc/modules
内核模块文件，里面列出的模块会在系统启动时自动加载。可用modconf工具配置，也可用文本编辑器配置。
9. /etc/gdm.conf
GDM配置文件
10. /etc/kde3/kdm/kdmrc
kdm的配置文件，默认kdm是不允许root用户登录的，如果我们需以root用户登录，我们需修改kdmrc文件，把
AllowRootLogin=false
改为
AllowRootLogin=true
11. /etc/services
Internet网络服务文件，记录网络服务名和它们对应使用的端口号及协议。文件中的每一行对应一种服务，它由4个字段组成，中间用TAB或空格分隔，分别表示“服务名称”、“使用端口”、“协议名称”以及“别名”。下面是这个文件的节选内容。
tcpmux 1/tcp # TCP port service multiplexer
echo 7/tcp
echo 7/udp
discard 9/tcp sink null
discard 9/udp sink null
systat 11/tcp users
daytime 13/tcp
daytime 13/udp
netstat 15/tcp
qotd 17/tcp quote
msp 18/tcp # message send protocol
msp 18/udp
chargen 19/tcp ttytst source
chargen 19/udp ttytst source
ftp-data 20/tcp
ftp 21/tcp
fsp 21/udp fspd
ssh 22/tcp # SSH Remote Login Protocol
ssh 22/udp
telnet 23/tcp
smtp 25/tcp mail
time 37/tcp timserver
一般情况下，不要修改该文件的内容，因为这些设置都是Internet标准的设置。一旦修改，可能会造成系统冲突，使用户无法正常访问资源。
Linux系统的端口号的范围为0--65535，不同范围有不同的意义。
0 不使用
1--1023 系统保留，只能由root用户使用
1024---4999 由客户端程序自由分配
5000---65535 由服务器端程序自由分配
12. /etc/protocols
该文件是网络协议定义文件，里面记录了TCP/IP协议族的所有协议类型。文件中的每一行对应一个协议类型，它有3个字段，中间用TAB或空格分隔，分别表示“协议名称”、“协议号”和“协议别名”。下面是该文件的节选内容。
# Internet (IP) protocols
#
# Updated from http://www.iana.org/assignments/protocol-numbers and other
# sources.
# New protocols will be added on request if they have been officially
# assigned by IANA and are not historical.
# If you need a huge list of used numbers please install the nmap package.

ip 0 IP # internet protocol, pseudo protocol number
#hopopt 0 HOPOPT # IPv6 Hop-by-Hop Option [RFC1883]
icmp 1 ICMP # internet control message protocol
igmp 2 IGMP # Internet Group Management
ggp 3 GGP # gateway-gateway protocol
ipencap 4 IP-ENCAP # IP encapsulated in IP (officially ``IP'')
st 5 ST # ST datagram mode
tcp 6 TCP # transmission control protocol
egp 8 EGP # exterior gateway protocol
igp 9 IGP # any private interior gateway (Cisco)
pup 12 PUP # PARC universal packet protocol
不要对该文件进行任何修改。
13. /etc/network/interfaces
网络接口参数配置文件，下面是一个配置示例，它在一个网络接口中配置了两个静态IP地址：
# /etc/network/interfaces -- configuration file for ifup(8), ifdown(8)

# The loopback interface
auto lo
iface lo inet loopback

# The first network card - this entry was created during the Debian installation
# (network, broadcast and gateway are optional)
auto eth0
iface eth0 inet static
address 192.168.1.1
netmask 255.255.255.0
network 192.168.1.0
broadcast 192.168.1.255
# gateway 192.168.1.1
# name 以太网局域网网卡

auto eth0:0
iface eth0:0 inet static
address 192.168.1.2
netmask 255.255.255.0
network 192.168.1.0
broadcast 192.168.1.255
gateway 192.168.1.1
下面是一个从DHCP服务器自动获得IP地址的示例：
# /etc/network/interfaces -- configuration file for ifup(8), ifdown(8)

# The loopback interface
auto lo
iface lo inet loopback

# The first network card - this entry was created during the Debian installation
# (network, broadcast and gateway are optional)
auto eth0
iface eth0 inet dhcp
14. /etc/resolv.conf
该文件是DNS域名解析的配置文件，它的格式很简单，每行以一个关键字开头，后接配置参数。resolv.conf的关键字主要有四个，分别是：
nameserver #定义DNS服务器的IP地址
domain #定义本地域名
search #定义域名的搜索列表
sortlist #对返回的域名进行排序
/etc/resolv.conf的一个示例：
domain ringkee.com
search www.ringkee.com ringkee.com
nameserver 202.96.128.86
nameserver 202.96.128.166
最主要是nameserver关键字，如果没指定nameserver就找不到DNS服务器，其它关键字是可选的。
15. /etc/host.conf
当系统中同时存在DNS域名解析和/etc/hosts主机表机制时，由该/etc/host.conf确定主机名解释顺序。示例：
order hosts,bind #名称解释顺序
multi on #允许主机拥有多个IP地址
nospoof on #禁止IP地址欺骗
order是关键字，定义先用本机hosts主机表进行名称解释，如果不能解释，再搜索bind名称服务器(DNS)。
16. /etc/hosts
设置IP地址与主机名对应表，可用该文件来进行主机名称解释。如：
#格式：IP地址 主机名 别名
127.0.0.1 localhost localhost.localdomain
192.168.1.1 debian debian
192.168.0.2 t02 t02.tiger
192.168.0.4 t04 t04.tiger
17. /etc/hostname
该文件只有一行，记录着本机的主机名。
18. /etc/hosts.allow和/etc/hosts.deny
这两个文件是tcpd服务器的配置文件，tcpd服务器可以控制外部IP对本机服务的访问。这两个配置文件的格式如下：
#服务进程名:主机列表:当规则匹配时可选的命令操作
server_name:hosts-list[:command]
/etc/hosts.allow控制可以访问本机的IP地址，/etc/hosts.deny控制禁止访问本机的IP。如果两个文件的配置有冲突，以/etc/hosts.deny为准。下面是一个/etc/hosts.allow的示例：
ALL:127.0.0.1 #允许本机访问本机所有服务进程
smbd:192.168.0.0/255.255.255.0 #允许192.168.0.网段的IP访问smbd服务
ALL关键字匹配所有情况，EXCEPT匹配除了某些项之外的情况，PARANOID匹配你想控制的IP地址和它的域名不匹配时(域名伪装)的情况。
```

