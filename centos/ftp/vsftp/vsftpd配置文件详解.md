```shell
================================================================================

https://www.cnblogs.com/acpp/archive/2010/02/08/1666054.html

vsftpd配置文件详解
1.默认配置：
1>允许匿名用户和本地用户登陆。
     anonymous_enable=YES
     local_enable=YES
2>匿名用户使用的登陆名为ftp或anonymous，口令为空；匿名用户不能离开匿名  用户家目录/var/ftp,且只能下载不能上传。
3>本地用户的登录名为本地用户名，口令为此本地用户的口令；本地用户可以在自己家目录中进行读写操作；本地用户可以离开自家目录切换至有权限访问的其他目录，并在权限允许的情况下进行上传/下载。
    write_enable=YES
4>写在文件/etc/vsftpd.ftpusers中的本地用户禁止登陆。
           
2.配置文件格式： 
vsftpd.conf 的内容非常单纯，每一行即为一项设定。若是空白行或是开头为#的一行，将会被忽略。内容的格式只有一种，如下所示
option=value
要注意的是，等号两边不能加空白。
 
3.匿名用户（anonymous）设置
anonymous_enable=YES/NO（YES）
控制是否允许匿名用户登入，YES 为允许匿名登入，NO 为不允许。默认值为YES。
write_enable=YES/NO（YES）
是否允许登陆用户有写权限。属于全局设置，默认值为YES。
no_anon_password=YES/NO（NO）
若是启动这项功能，则使用匿名登入时，不会询问密码。默认值为NO。
ftp_username=ftp
定义匿名登入的使用者名称。默认值为ftp。
anon_root=/var/ftp
使用匿名登入时，所登入的目录。默认值为/var/ftp。注意ftp目录不能是777的权限属性，即匿名用户的家目录不能有777的权限。
anon_upload_enable=YES/NO（NO）
如果设为YES，则允许匿名登入者有上传文件（非目录）的权限，只有在write_enable=YES时，此项才有效。当然，匿名用户必须要有对上层目录的写入权。默认值为NO。
anon_world_readable_only=YES/NO（YES）
如果设为YES，则允许匿名登入者下载可阅读的档案（可以下载到本机阅读，不能直接在FTP服务器中打开阅读）。默认值为YES。
anon_mkdir_write_enable=YES/NO（NO）
如果设为YES，则允许匿名登入者有新增目录的权限，只有在write_enable=YES时，此项才有效。当然，匿名用户必须要有对上层目录的写入权。默认值为NO。
anon_other_write_enable=YES/NO（NO）
如 果设为YES，则允许匿名登入者更多于上传或者建立目录之外的权限，譬如删除或者重命名。（如果anon_upload_enable=NO，则匿名用户 不能上传文件，但可以删除或者重命名已经存在的文件；如果anon_mkdir_write_enable=NO，则匿名用户不能上传或者新建文件夹，但 可以删除或者重命名已经存在的文件夹。）默认值为NO。
chown_uploads=YES/NO（NO）
设置是否改变匿名用户上传文件（非目录）的属主。默认值为NO。
chown_username=username
设置匿名用户上传文件（非目录）的属主名。建议不要设置为root。
anon_umask=077
设置匿名登入者新增或上传档案时的umask 值。默认值为077，则新建档案的对应权限为700。
deny_email_enable=YES/NO（NO）
若是启动这项功能，则必须提供一个档案/etc/vsftpd/banner_emails，内容为email address。若是使用匿名登入，则会要求输入email address，若输入的email address 在此档案内，则不允许进入。默认值为NO。
banned_email_file=/etc/vsftpd/banner_emails
此文件用来输入email address，只有在deny_email_enable=YES时，才会使用到此档案。若是使用匿名登入，则会要求输入email address，若输入的email address 在此档案内，则不允许进入。
 
4.本地用户设置
local_enable=YES/NO（YES）
控制是否允许本地用户登入，YES 为允许本地用户登入，NO为不允许。默认值为YES。
local_root=/home/username
当本地用户登入时，将被更换到定义的目录下。默认值为各用户的家目录。
write_enable=YES/NO（YES）
是否允许登陆用户有写权限。属于全局设置，默认值为YES。
local_umask=022
本地用户新增档案时的umask 值。默认值为077。
file_open_mode=0755
本地用户上传档案后的档案权限，与chmod 所使用的数值相同。默认值为0666。
 
5.欢迎语设置
dirmessage_enable=YES/NO（YES）
如果启动这个选项，那么使用者第一次进入一个目录时，会检查该目录下是否有.message这个档案，如果有，则会出现此档案的内容，通常这个档案会放置欢迎话语，或是对该目录的说明。默认值为开启。
message_file=.message
设置目录消息文件，可将要显示的信息写入该文件。默认值为.message。
banner_file=/etc/vsftpd/banner
当使用者登入时，会显示此设定所在的档案内容，通常为欢迎话语或是说明。默认值为无。如果欢迎信息较多，则使用该配置项。
ftpd_banner=Welcome to BOB's FTP server
这里用来定义欢迎话语的字符串，banner_file是档案的形式，而ftpd_banner 则是字符串的形式。预设为无。
 
6.控制用户是否允许切换到上级目录
在默认配置下，本地用户登入FTP后可以使用cd命令切换到其他目录，这样会对系统带来安全隐患。可以通过以下三条配置文件来控制用户切换目录。
chroot_list_enable=YES/NO（NO）
设置是否启用chroot_list_file配置项指定的用户列表文件。默认值为NO。
chroot_list_file=/etc/vsftpd.chroot_list
用于指定用户列表文件，该文件用于控制哪些用户可以切换到用户家目录的上级目录。
chroot_local_user=YES/NO（NO）
用于指定用户列表文件中的用户是否允许切换到上级目录。默认值为NO。
通过搭配能实现以下几种效果：
①当chroot_list_enable=YES，chroot_local_user=YES时，在/etc/vsftpd.chroot_list文件中列出的用户，可以切换到其他目录；未在文件中列出的用户，不能切换到其他目录。
②当chroot_list_enable=YES，chroot_local_user=NO时，在/etc/vsftpd.chroot_list文件中列出的用户，不能切换到其他目录；未在文件中列出的用户，可以切换到其他目录。
③当chroot_list_enable=NO，chroot_local_user=YES时，所有的用户均不能切换到其他目录。
④当chroot_list_enable=NO，chroot_local_user=NO时，所有的用户均可以切换到其他目录。
 
7.数据传输模式设置
FTP在传输数据时，可以使用二进制方式，也可以使用ASCII模式来上传或下载数据。
ascii_upload_enable=YES/NO（NO）
设置是否启用ASCII 模式上传数据。默认值为NO。
ascii_download_enable=YES/NO（NO）
设置是否启用ASCII 模式下载数据。默认值为NO。
 
8.访问控制设置
两种控制方式：一种控制主机访问，另一种控制用户访问。
①控制主机访问：
tcp_wrappers=YES/NO（YES）
设 置vsftpd是否与tcp wrapper相结合来进行主机的访问控制。默认值为YES。如果启用，则vsftpd服务器会检查/etc/hosts.allow 和/etc/hosts.deny 中的设置，来决定请求连接的主机，是否允许访问该FTP服务器。这两个文件可以起到简易的防火墙功能。
比如：若要仅允许192.168.0.1—192.168.0.254的用户可以连接FTP服务器，则在/etc/hosts.allow文件中添加以下内容：
vsftpd:192.168.0. :allow
all:all :deny
②控制用户访问：
对于用户的访问控制可以通过/etc目录下的vsftpd.user_list和ftpusers文件来实现。
userlist_file=/etc/vsftpd.user_list
控制用户访问FTP的文件，里面写着用户名称。一个用户名称一行。
userlist_enable=YES/NO（NO）
是否启用vsftpd.user_list文件。
userlist_deny=YES/NO（YES）
决定vsftpd.user_list文件中的用户是否能够访问FTP服务器。若设置为YES，则vsftpd.user_list文件中的用户不允许访问FTP，若设置为NO，则只有vsftpd.user_list文件中的用户才能访问FTP。
/etc /vsftpd/ftpusers文件专门用于定义不允许访问FTP服务器的用户列表（注意:如果 userlist_enable=YES,userlist_deny=NO,此时如果在vsftpd.user_list和ftpusers中都有某个 用户时，那么这个用户是不能够访问FTP的，即ftpusers的优先级要高）。默认情况下vsftpd.user_list和ftpusers，这两个 文件已经预设置了一些不允许访问FTP服务器的系统内部账户。如果系统没有这两个文件，那么新建这两个文件，将用户添加进去即可。
 
9.访问速率设置
anon_max_rate=0
设置匿名登入者使用的最大传输速度，单位为B/s，0 表示不限制速度。默认值为0。
local_max_rate=0
本地用户使用的最大传输速度，单位为B/s，0 表示不限制速度。预设值为0。
 
10.超时时间设置
accept_timeout=60
设置建立FTP连接的超时时间，单位为秒。默认值为60。
connect_timeout=60
PORT 方式下建立数据连接的超时时间，单位为秒。默认值为60。
data_connection_timeout=120
设置建立FTP数据连接的超时时间，单位为秒。默认值为120。
idle_session_timeout=300
设置多长时间不对FTP服务器进行任何操作，则断开该FTP连接，单位为秒。默认值为300 。
 
11.日志文件设置
xferlog_enable= YES/NO（YES）
是否启用上传/下载日志记录。如果启用，则上传与下载的信息将被完整纪录在xferlog_file 所定义的档案中。预设为开启。
xferlog_file=/var/log/vsftpd.log
设置日志文件名和路径，默认值为/var/log/vsftpd.log。
xferlog_std_format=YES/NO（NO）
如果启用，则日志文件将会写成xferlog的标准格式，如同wu-ftpd 一般。默认值为关闭。
log_ftp_protocol=YES|NO（NO）
如果启用此选项，所有的FTP请求和响应都会被记录到日志中，默认日志文件在/var/log/vsftpd.log。启用此选项时，xferlog_std_format不能被激活。这个选项有助于调试。默认值为NO。
 
12.定义用户配置文件
在vsftpd中，可以通过定义用户配置文件来实现不同的用户使用不同的配置。
user_config_dir=/etc/vsftpd/userconf
设置用户配置文件所在的目录。当设置了该配置项后，用户登陆服务器后，系统就会到/etc/vsftpd/userconf目录下，读取与当前用户名相同的文件，并根据文件中的配置命令，对当前用户进行更进一步的配置。
例 如：定义user_config_dir=/etc/vsftpd/userconf，且主机上有使用者 test1,test2，那么我们就在user_config_dir 的目录新增文件名为test1和test2两个文件。若是test1 登入，则会读取user_config_dir 下的test1 这个档案内的设定。默认值为无。利用用户配置文件，可以实现对不同用户进行访问速度的控制，在各用户配置文件中定义local_max_rate=XX， 即可。
 
13.FTP的工作方式与端口设置
FTP有两种工作方式：PORT FTP（主动模式）和PASV FTP（被动模式）
listen_port=21
设置FTP服务器建立连接所监听的端口，默认值为21。
connect_from_port_20=YES/NO
指定FTP使用20端口进行数据传输，默认值为YES。
ftp_data_port=20
设置在PORT方式下，FTP数据连接使用的端口，默认值为20。
pasv_enable=YES/NO（YES）
若设置为YES，则使用PASV工作模式；若设置为NO，则使用PORT模式。默认值为YES，即使用PASV工作模式。
pasv_max_port=0
在PASV工作模式下，数据连接可以使用的端口范围的最大端口，0 表示任意端口。默认值为0。
pasv_min_port=0
在PASV工作模式下，数据连接可以使用的端口范围的最小端口，0 表示任意端口。默认值为0。
 
14.与连接相关的设置
listen=YES/NO（YES）
设 置vsftpd服务器是否以standalone模式运行。以standalone模式运行是一种较好的方式，此时listen必须设置为YES，此为默 认值。建议不要更改，有很多与服务器运行相关的配置命令，需要在此模式下才有效。若设置为NO，则vsftpd不是以独立的服务运行，要受到xinetd 服务的管控，功能上会受到限制。
max_clients=0
设置vsftpd允许的最大连接数，默认值为0，表示不受限制。若设置为100时，则同时允许有100个连接，超出的将被拒绝。只有在standalone模式运行才有效。
max_per_ip=0
设置每个IP允许与FTP服务器同时建立连接的数目。默认值为0，表示不受限制。只有在standalone模式运行才有效。
listen_address=IP地址
设置FTP服务器在指定的IP地址上侦听用户的FTP请求。若不设置，则对服务器绑定的所有IP地址进行侦听。只有在standalone模式运行才有效。
setproctitle_enable=YES/NO（NO）
设置每个与FTP服务器的连接，是否以不同的进程表现出来。默认值为NO，此时使用ps aux |grep ftp只会有一个vsftpd的进程。若设置为YES，则每个连接都会有一个vsftpd的进程。
 
15.虚拟用户设置
虚拟用户使用PAM认证方式。
pam_service_name=vsftpd
设置PAM使用的名称，默认值为/etc/pam.d/vsftpd。
guest_enable= YES/NO（NO）
启用虚拟用户。默认值为NO。
guest_username=ftp
这里用来映射虚拟用户。默认值为ftp。
virtual_use_local_privs=YES/NO（NO）
当该参数激活（YES）时，虚拟用户使用与本地用户相同的权限。当此参数关闭（NO）时，虚拟用户使用与匿名用户相同的权限。默认情况下此参数是关闭的（NO）。
 
16.其他设置
text_userdb_names= YES/NO（NO）
设置在执行ls –la之类的命令时，是显示UID、GID还是显示出具体的用户名和组名。默认值为NO，即以UID和GID方式显示。若希望显示用户名和组名，则设置为YES。
ls_recurse_enable=YES/NO（NO）
若是启用此功能，则允许登入者使用ls –R（可以查看当前目录下子目录中的文件）这个指令。默认值为NO。
hide_ids=YES/NO（NO）
如果启用此功能，所有档案的拥有者与群组都为ftp，也就是使用者登入使用ls -al之类的指令，所看到的档案拥有者跟群组均为ftp。默认值为关闭。
download_enable=YES/NO（YES）
如果设置为NO，所有的文件都不能下载到本地，文件夹不受影响。默认值为YES。
 
 
 
 
文中有不对或者有不清楚的地方，请大家告诉我，谢谢！
 
vsftp实例
 
在 众多网络应用中，FTP（文件传输协议）有着非常重要的地位。它可以用于文件的存储和共享。与大多数Internet服务一样，FTP也是一个客户机/服 务器系统。用户通过一个支持FTP协议的客户机程序，连接到主机上的FTP服务器程序。用户通过客户机程序向服务器程序发出命令，服务器程序执行用户发出 的命令，并将执行结果返回给客户机。
 
现在我们通过一个例子来看看FTP是如何搭建起来的。在此，我们用著名的Vsftp来搭建FTP服务器。此FTP服务器主要用于公司内部文件的存储和共享。
 
一．需求
1. 某公司有5个大部门，分别为：人事行政部、财务部、技术部、市场部、生产部。
2. 各部门的文件夹只允许本部门员工有权访问；各部门之间交流性质的文件放到公用文件夹中。
3. 每个部门都有一个管理本部门文件夹的管理员账号和一个只能上传、下载和查看文件的普通用户权限的账号。
4. 公用文件夹中分为存放工具的文件夹和存放文件的文件夹。
5. 用户只能对自己的家目录文件夹及其下面的目录文件有操作权限，不允许切换到上级目录，不允许匿名用户访问。
 
二．规划
根据公司需求情况，现做出如下规划：
1. 在系统分区时单独分一个Company的区，在该区下有以下几个文件夹：HR、CaiWu、JiShu、ShiChang、ShengChan和 Share。在Share下又有以下几个文件夹：HR、CaiWu、JiShu、ShiChang、ShengChan和Tools。
2. 各部门对应的文件夹由各部门自己管理，Tools文件夹由管理员维护。
3. HR管理员账号：hradmin；普通用户账号：hruser。
   CaiWu管理员账号：caiwuadmin；普通用户账号：caiwuuser。
   JiShu管理员账号：jishuadmin；普通用户账号：jishuuser。
   ShiChang管理员账号：shichangadmin；普通用户账号：shichanguser。
   ShengChan管理员账号：shengchanadmin；普通用户账号：shengchanuser。
   Tools管理员账号：admin。
4. 各部门管理员账号有完全控制本部门文件夹的权限以及下载Tools文件夹中工具的权限，普通用户账号只有上传、下载和查看本部门文件夹的权限以及下载Tools文件夹中工具的权限。
5. 因为此FTP服务器主要用于公司内部使用，因此，FTP使用主动工作模式，在FTP服务器上将其他不需要用到的端口屏蔽掉，增加服务器安全。
 
文件夹之间的关系请见下图：
 
 
三．Vsftp RPM安装和启动
在系统中使用rpm –qa |grep vsftp来查看系统有没有安装该软件，如果没有安装则挂载系统盘，找到vsftp软件包，使用rpm –ivh vsftp*即可安装。
使用RPM包安装后，vsftp的配置文件默认在/etc/vsftpd/下。
使用service vsftpd start启动vsftp。Vsftp默认允许匿名用户访问。
使用chkconfig --level 35 vsftpd on，可以使vsftp随系统一起启动。
在/etc/sysconfig/iptables中将20和21端口开放，然后用service iptables restart重启iptables服务。
在/etc/selinux/config中将“SELINUX”项关闭，SELINUX=disabled。
 
四．新建用户
使用useradd命令新建用户，使用passwd命令添加密码。
useradd hradmin –r –m –d /Company/ –s /sbin/nologin
useradd hruser –r –m –g hradmin –d /Company/ –s /sbin/nologin
useradd caiwuadmin –r –m –d /Company/ –s /sbin/nologin
useradd caiwuuser –r –m –g caiwuadmin –d /Company/ –s /sbin/nologin
useradd jishuadmin –r –m –d /Company/ –s /sbin/nologin
useradd jishuuser –r –m –g jishuadmin –d /Company/ –s /sbin/nologin
useradd shichangadmin –r –m –d /Company/ –s /sbin/nologin
useradd shichanguser –r –m –g shichangadmin –d /Company/ –s /sbin/nologin
useradd shengchanadmin –r –m –d /Company/ –s /sbin/nologin
useradd shengchanuser –r –m –g shangchanadmin –d /Company/ –s /sbin/nologin
useradd admin –r –m –g root –d /Company/Share/Tools
 
五．新建目录
在/Company中添加各部门的私密文件夹以及一个用于放置共享东西的共享文件夹。
cd /Company/
mkdir HR CaiWu JiShu ShiChang ShengChan Share
在/Company下的共享文件夹中，添加各部门的文件夹以及一个放置共享工具的文件夹，这些部门文件夹用于放置需要共享的文件。
cd /Company/Share/
mkdir HR CaiWu JiShu ShiChang ShengChan Tools
 
六．修改目录属性
修 改/Company中的各部门文件夹的文件权限为1770（实现效果：本部门管理员和普通用户可以进入，非本部门用户禁止进入；本部门管理员上传的文件， 本部门的普通用户只能下载和查看，不能修改；本部门普通用户上传的文件，本部门的管理员可以查看，下载，删除，重命名，但是不能修改里面的内容；如果管理 员想要修改普通用户上传的文件，可以先下载该文件，然后在FTP上删除该文件，在本机编辑好后再将该文件上传），属主和组为各部门的管理员及管理员组。
cd /Company/
chmod –R 1770 HR CaiWu JiShu ShiChang ShengChan
chown –R hradmin.hradmin HR/
chown –R caiwuadmin.caiwuadmin CaiWu/
chown –R jishuadmin.jishuadmin JiShu/
chown –R shichangadmin.shichangadmin ShiChang/
chown –R shengchanadmin.shengchanadmin ShengChan/
chmod –R 1775 Share/
chown admin.root Share/
 
cd /Company/Share
chown –R hradmin.hradmin HR/
chown –R caiwuadmin.caiwuadmin CaiWu/
chown –R jishuadmin.jishuadmin JiShu/
chown –R shichangadmin.shichangadmin ShiChang/
chown –R shengchanadmin.shengchanadmin ShengChan/
chown –R admin.root Tools/
 
七．配置vsftp
Vsftp的配置文件在/etc/vsftpd/下。文件名为vsftpd.conf。
cd /etc/vsftpd/
cp vsftpd.conf vsftpd.conf.bak
vi vsftpd.conf
write_enable=YES #允许登入者有写权限
anonymous_enable=NO #禁止匿名用户访问
local_enable=YES #允许本地用户访问
local_umask=022 #本地用户新增档案时的umask值
file_open_mode=0755 #本地用户上传档案后的档案权限
ftpd_banner=Welcome to BOB's FTP server. #定义欢迎话语的字符串
xferlog_enable=YES #启用上传/下载日志记录
xferlog_file=/var/log/vsftpd.log #日志文件所在的路径及名称
xferlog_std_format=YES #将日志文件写成xferlog的标准格式
ascii_upload_enable=YES #启用ASCII 模式上传数据
ascii_download_enable=YES #启用ASCII 模式下载数据
chroot_list_enable=YES #在chroot_list中列出的用户不允许切换到家目录的上级目录
chroot_local_user=NO #
chroot_list_file=/etc/vsftpd/chroot_list #
userlist_enable=YES #在user_list中列出的用户不能访问FTP服务器，未列出的可以访问
userlist_deny=YES #
userlist_file=/etc/vsftpd/user_list #
tcp_wrappers=NO #不使用tcp wrapper来控制主机访问
setproctitle_enable=YES #每个与FTP服务器的连接，都以不同的进程表现出来
listen=YES #FTP服务器以standalone模式运行
port_enable=YES #FTP服务器启用PORT模式
pasv_enable=NO #禁用FTP服务器的PASV模式
listen_port=21 #FTP服务器监听21端口
connect_from_port_20=YES #指定FTP服务器使用20端口进行数据传输
ftp_data_port=20 #FTP服务器数据传输端口为20
idle_session_timeout=600 #600秒钟不对FTP服务器进行任何操作，则断开该FTP连接
data_connection_timeout=120 #建立FTP数据连接的超时时间为120秒
max_clients=0 #不限制用户的连接数量
max_per_ip=3 #每个IP只能与FTP服务器同时建立3个连接
local_max_rate=512000 #本地用户使用的最大传输速度
 
编辑chroot_list，一个用户一行。此文件中列出的用户不允许访问其家目录的上级目录。
vi /etc/vsftpd/chroot_list
hradmin
hruser
caiwuadmin
caiwuuser
jishuadmin
jishuuesr
shichangadmin
shichanguser
shengchanadmin
shengchanuser
 
编辑user_list，一个用户一行。在此文件中列出的用户不能访问FTP服务器，未列出的可以访问。
vi /etc/vsftpd/user_list
 
八．测试
使用flashFXP软件作为FTP客户端。
```

