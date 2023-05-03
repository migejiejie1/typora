```shell
Linux搭建FTP的vsftpd.conf文件配置详解

锁定用户的家目录

[root@localhost home]# vim /etc/vsftpd/vsftpd.conf 把其中的某些注释取消
chroot_local_user=YES                                              ####锁定本地用户的家目录，但是目录本身的w权限要取消。这是安全考虑，若不取消会出问题 , 或
allow_writeable_chroot=YES
# chroot_list_enable=YES                                             ####启用可跳出家目录列表
# chroot_list_file=/etc/vsftpd/chroot_list                           ####跳出家目录文件
比如我们想锁定vsftp在家目录，但是想让123用户能跳出家目录，就可以把123用户写入chroot_list文件

1.默认配置
根据默认配置给出中文注释

# Example config file /etc/vsftpd.conf
#
# The default compiled in settings are fairly paranoid. This sample file
# loosens things up a bit, to make the ftp daemon more usable.
# Please see vsftpd.conf.5 for all compiled in defaults.
#
# READ THIS: This example file is NOT an exhaustive list of vsftpd options.
# Please read the vsftpd.conf.5 manual page to get a full idea of vsftpd’s
# capabilities.
#
#
# Run standalone? vsftpd can run either from an inetd or as a standalone
# daemon started from an initscript.
listen=YES （绑定到listen_port指定的端口,既然都绑定了也就是每时都开着的,就是standalone模式）
#
# This directive enables listening on IPv6 sockets. By default, listening
# on the IPv6 “any” address (::) will accept connections from both IPv6
# and IPv4 clients. It is not necessary to listen on *both* IPv4 and IPv6
# sockets. If you want that (perhaps because you want to listen on specific
# addresses) then you must run two copies of vsftpd with two configuration
# files.
#listen_ipv6=no
#
# Allow anonymous FTP? (Disabled by default).
anonymous_enable=NO （接受匿名用户，默认无密码请求）
#
# Uncomment this to allow local users to log in.
local_enable=YES （接受本地用户）
#
# Uncomment this to enable any form of FTP write command.
write_enable=YES (上传总开关)
#
# Default umask for local users is 077. You may wish to change this to 022,
# if your users expect that (022 is used by most other ftpd’s)
local_umask=022 （本地用户新增档案的权限）
#
# Uncomment this to allow the anonymous FTP user to upload files. This only
# has an effect if the above global write enable is activated. Also, you will
# obviously need to create a directory writable by the FTP user.
#anon_upload_enable=YES （允许匿名用户上传文件）
#
# Uncomment this if you want the anonymous FTP user to be able to create
# new directories.
#anon_mkdir_write_enable=YES （允许匿名用户创建新目录）
#
# Activate directory messages – messages given to remote users when they
# go into a certain directory.
dirmessage_enable=YES （允许为目录配置显示信息,显示每个目录下面的message_file文件的内容）
#
# If enabled, vsftpd will display directory listings with the time
# in your local time zone. The default is to display GMT. The
# times returned by the MDTM FTP command are also affected by this
# option.
use_localtime=YES
#
# Activate logging of uploads/downloads.
xferlog_enable=YES （开启日记功能 ）
#
# Make sure PORT transfer connections originate from port 20 (ftp-data).
connect_from_port_20=YES
#
# If you want, you can arrange for uploaded anonymous files to be owned by
# a different user. Note! Using “root” for uploaded files is not
# recommended!
#chown_uploads=YES （所有匿名上传的文件的所属用户将会被更改成chown_username）
#chown_username=whoever （匿名上传文件所属用户名）
#
# You may override where the log file goes if you like. The default is shown
# below.
xferlog_file=/var/log/vsftpd.log （日志文件位置 ）
#
# If you want, you can have your log file in standard ftpd xferlog format.
# Note that the default log file location is /var/log/xferlog in this case.
#xferlog_std_format=YES （使用标准格式 ）
#
# You may change the default value for timing out an idle session.
#idle_session_timeout=600 （空闲连接超时 ）
#
# You may change the default value for timing out a data connection.
#data_connection_timeout=120 （数据传输超时 ）
#
# It is recommended that you define on your system a unique user which the
# ftp server can use as a totally isolated and unprivileged user.
#nopriv_user=ftpsecure （当服务器运行于最底层时使用的用户名）
#
# Enable this and the server will recognise asynchronous ABOR requests. Not
# recommended for security (the code is non-trivial). Not enabling it,
# however, may confuse older FTP clients.
#async_abor_enable=YES （允许使用\”async ABOR\”命令,一般不用,容易出问题）
#
# By default the server will pretend to allow ASCII mode but in fact ignore
# the request. Turn on the below options to have the server actually do ASCII
# mangling on files when in ASCII mode.
# Beware that on some FTP servers, ASCII support allows a denial of service
# attack (DoS) via the command “SIZE /big/file” in ASCII mode. vsftpd
# predicted this attack and has always been safe, reporting the size of the
# raw file.
# ASCII mangling is a horrible feature of the protocol.
#ascii_upload_enable=YES （管控是否可用ASCII 模式上传。默认值为NO）
#ascii_download_enable=YES （管控是否可用ASCII 模式下载。默认值为NO）
#
# You may fully customise the login banner string:
#ftpd_banner=Welcome to blah FTP service. （login时显示欢迎信息.如果设置了banner_file则此设置无效）
#
# You may specify a file of disallowed anonymous e-mail addresses. Apparently
# useful for combatting certain DoS attacks.
#deny_email_enable=YES （如果匿名用户需要密码,那么使用banned_email_file里面的电子邮件地址的用户不能登录）
# (default follows)
#banned_email_file=/etc/vsftpd.banned_emails （禁止使用匿名用户登陆时作为密码的电子邮件地址）
#
# You may restrict local users to their home directories. See the FAQ for
# the possible risks in this before using chroot_local_user or
# chroot_list_enable below.
chroot_local_user=YES
#
# You may specify an explicit list of local users to chroot() to their home
# directory. If chroot_local_user is YES, then this list becomes a list of
# users to NOT chroot().
# (Warning! chroot’ing can be very dangerous. If using chroot, make sure that
# the user does not have write access to the top level directory within the
# chroot)
#chroot_local_user=YES
#chroot_list_enable=YES （如果启动这项功能，则所有列在chroot_list_file中的使用者不能更改根目录）
# (default follows)
#chroot_list_file=/etc/vsftpd.chroot_list （定义不能更改用户主目录的文件）
#
# You may activate the “-R” option to the builtin ls. This is disabled by
# default to avoid remote users being able to cause excessive I/O on large
# sites. However, some broken FTP clients such as “ncftp” and “mirror” assume
# the presence of the “-R” option, so there is a strong case for enabling it.
#ls_recurse_enable=YES （是否能使用ls -R命令以防止浪费大量的服务器资源）
#
# Customization
#
# Some of vsftpd’s settings don’t fit the filesystem layout by
# default.
#
# This option should be the name of a directory which is empty. Also, the
# directory should not be writable by the ftp user. This directory is used
# as a secure chroot() jail at times vsftpd does not require filesystem
# access.
secure_chroot_dir=/var/run/vsftpd/empty
#
# This string is the name of the PAM service vsftpd will use.
pam_service_name=vsftpd （定义PAM 所使用的名称，预设为vsftpd）
#
# This option specifies the location of the RSA certificate to use for SSL
# encrypted connections.
rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
ssl_enable=NO

#
# Uncomment this to indicate that vsftpd use a utf8 filesystem.
#utf8_filesystem=YES

 

2.过滤配置文件多余注释
过滤掉那些注释，以便我们日后修改配置，大家可以删除vsftpd.conf内容，拷贝以下：

anonymous_enable=YES

local_enable=YES

write_enable=YES

local_umask=022

#anon_upload_enable=YES

#anon_mkdir_write_enable=YES

dirmessage_enable=YES

xferlog_enable=YES

connect_from_port_20=YES

#chown_uploads=YES

#chown_username=whoever

#xferlog_file=/var/log/vsftpd.log

xferlog_std_format=YES

#idle_session_timeout=600

#data_connection_timeout=120

#nopriv_user=ftpsecure

#async_abor_enable=YES

#ascii_upload_enable=YES

#ascii_download_enable=YES

#ftpd_banner=Welcome to blah FTP service.

#deny_email_enable=YES

#banned_email_file=/etc/vsftpd/banned_emails

#chroot_list_enable=YES

#chroot_list_file=/etc/vsftpd/chroot_list

#ls_recurse_enable=YES

listen=YES

#listen_ipv6=YES

pam_service_name=vsftpd

userlist_enable=YES

tcp_wrappers=YES

3.vsftpd.conf支持特性详解
#################用户选项############### 

接受匿名用户 
anonymous_enable=YES 

#匿名用户login时不询问口令 
no_anon_password=YES

#匿名用户主目录 
anon_root=(none) 

#接受本地用户 
local_enable=YES 

#本地用户主目录 
local_root=(none) 

#如果匿名用户需要密码,那么使用banned_email_file里面的电子邮件地址的用户不能登录 
deny_email_enable=YES 

#仅在没有pam验证版本时有用,是否检查用户有一个有效的shell来登录 
check_shell=YES 

#若启用此选项,userlist_deny选项才被启动 
userlist_enable=YES 

#若为YES,则userlist_file中的用户将不能登录,为NO则只有userlist_file的用户可以登录 
userlist_deny=NO 

#如果和chroot_local_user一起开启,那么用户锁定的目录来自/etc/passwd每个用户指定的目录(这个不是很清楚,很哪位熟悉的指点一下) 
passwd_chroot_enable=NO 

#定义匿名登入的使用者名称。默认值为ftp。 
ftp_username=FTP 

#################用户权限控制############### 

#可以上传(全局控制). 
write_enable=YES 

#本地用户上传文件的umask 
local_umask=022 

#上传文件的权限配合umask使用 
#file_open_mode=0666 

#匿名用户可以上传 
anon_upload_enable=NO 

#匿名用户可以建目录 
anon_mkdir_write_enable=NO

匿名用户其它的写权利(更改权限?) 
anon_other_write_enable=NO 

如果设为YES，匿名登入者会被允许下载可阅读的档案。默认值为YES。 
anon_world_readable_only=YES 

#如果开启,那么所有非匿名登陆的用户名都会被切换成guest_username指定的用户名 
#guest_enable=NO 

所有匿名上传的文件的所属用户将会被更改成chown_username 
chown_uploads=YES 

匿名上传文件所属用户名 
chown_username=lightwiter 

#如果启动这项功能，则所有列在chroot_list_file之中的使用者不能更改根目录 
chroot_list_enable=YES 

#允许使用\”async ABOR\”命令,一般不用,容易出问题 
async_abor_enable=YES 

管控是否可用ASCII 模式上传。默认值为NO。 
ascii_upload_enable=YES 

#管控是否可用ASCII 模式下载。默认值为NO。 
ascii_download_enable=YES 

#这个选项必须指定一个空的数据夹且任何登入者都不能有写入的权限，当vsftpd 不需要file system 的权限时，就会将使用者限制在此数据夹中。默认值为/usr/share/empty 
secure_chroot_dir=/usr/share/empty 

###################超时设置################## 

#空闲连接超时 
idle_session_timeout=600 

#数据传输超时 
data_connection_timeout=120 

#PAVS请求超时 
ACCEPT_TIMEOUT=60 

#PROT模式连接超时 
connect_timeout=60 

################服务器功能选项############### 

#开启日记功能 
xferlog_enable=YES 

#使用标准格式 
xferlog_std_format=YES 

#当xferlog_std_format关闭且本选项开启时,记录所有ftp请求和回复,当调试比较有用. 
#log_ftp_protocol=NO 

#允许使用pasv模式 
pasv_enable=YES 

#关闭安全检查,小心呀. 
#pasv_promiscuous+NO 

#允许使用port模式 
#port_enable=YES 

#关闭安全检查 
#prot_promiscuous 

#开启tcp_wrappers支持 
tcp_wrappers=YES 

#定义PAM 所使用的名称，预设为vsftpd。 
pam_service_name=vsftpd 

#当服务器运行于最底层时使用的用户名 
nopriv_user=nobody 

#使vsftpd在pasv命令回复时跳转到指定的IP地址.(服务器联接跳转?) 
pasv_address=(none) 

#################服务器性能选项##############

#是否能使用ls -R命令以防止浪费大量的服务器资源 
#ls_recurse_enable=YES 

#是否使用单进程模式 
#one_process_model 

#绑定到listen_port指定的端口,既然都绑定了也就是每时都开着的,就是那个什么standalone模式 
listen=YES 

#当使用者登入后使用ls -al 之类的指令查询该档案的管理权时，预设会出现拥有者的UID，而不是该档案拥有者的名称。若是希望出现拥有者的名称，则将此功能开启。 
text_userdb_names=NO 

#显示目录清单时是用本地时间还是GMT时间,可以通过mdtm命令来达到一样的效果 
use_localtime=NO 

#测试平台优化 
#use_sendfile=YES 

################信息类设置################ 

#login时显示欢迎信息.如果设置了banner_file则此设置无效 
ftpd_banner=欢迎来到湖南三辰Fake-Ta FTP 网站. 

#允许为目录配置显示信息,显示每个目录下面的message_file文件的内容 
dirmessage_enable=YES 

#显示会话状态信息,关! 
#setproctitle_enable=YES 

############## 文件定义 ################## 

#定义不能更改用户主目录的文件 
chroot_list_file=/etc/vsftpd/vsftpd.chroot_list 

#定义限制/允许用户登录的文件 
userlist_file=/etc/vsftpd/vsftpd.user_list 

#定义登录信息文件的位置 
banner_file=/etc/vsftpd/banner 

#禁止使用的匿名用户登陆时作为密码的电子邮件地址 
banned_email_file=/etc/vsftpd.banned_emails 

#日志文件位置 
xferlog_file=/var/log/vsftpd.log 

#目录信息文件 
message_file=.message 

############## 目录定义 ################# 

#定义用户配置文件的目录 
user_config_dir=/etc/vsftpd/userconf 

#定义本地用户登陆的根目录,注意定义根目录可以是相对路径也可以是绝对路径.相对路径是针对用户家目录来说的. 
local_root=webdisk #此项设置每个用户登陆后其根目录为/home/username/webdisk 

#匿名用户登陆后的根目录 
anon_root=/var/ftp 

#############用户连接选项################# 

#可接受的最大client数目 
max_clients=100 

#每个ip的最大client数目 
max_per_ip=5 

#使用标准的20端口来连接ftp 
connect_from_port_20=YES 

#绑定到某个IP,其它IP不能访问 
listen_address=192.168.0.2 

#绑定到某个端口 
#listen_port=2121 

#数据传输端口 
#ftp_data_port=2020 

#pasv连接模式时可以使用port 范围的上界，0 表示任意。默认值为0。 
pasv_max_port=0 

#pasv连接模式时可以使用port 范围的下界，0 表示任意。默认值为0。 
pasv_min_port=0 

##############数据传输选项################# 

#匿名用户的传输比率(b/s) 
anon_max_rate=51200 

#本地用户的传输比率(b/s) 
local_max_rate=5120000
```

