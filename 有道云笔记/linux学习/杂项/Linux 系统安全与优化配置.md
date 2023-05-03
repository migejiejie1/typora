```shell

Linux 系统安全与优化配置
Linux 系统安全问题
--------------------------------------------------------------------------------
目录
1. Openssh 安全配置
1.1. 禁止root用户登录
1.2. 限制SSH验证重试次数
1.3. 禁止证书登陆
1.4. 使用证书替代密码认证
1.5. 图形窗口客户端记忆密码的问题
1.6. 关闭 GSSAPI
1.7. 禁止SSH端口映射
1.8. IP地址限制
2. Shell 安全
2.1. .history 文件
2.2. sudo 安全问题
2.3. 临时文件安全
2.4. 执行权限
3. 防火墙
3.1. 策略
3.2. 防止成为跳板机
4. Linux 系统资源调配
4.1. /etc/security/limits.conf
4.2. 关闭写磁盘I/O功能
5. PAM 插件认证加固配置
5.1. pam_tally2.so
5.2. pam_listfile.so
5.3. pam_access.so
5.4. pam_wheel.so
1. Openssh 安全配置
这节主要讲与SSH有关的安全配置
1.1. 禁止root用户登录
只允许普通用户登陆，然后通过su命令切换到root用过。后面还会将怎样限制su命令
			PermitRootLogin no			
1.2. 限制SSH验证重试次数
超过3次socket连接会断开，效果不明显，有一点点用。
			MaxAuthTries 3			
1.3. 禁止证书登陆
证书登陆非常安全，但是很有可能正常用户在你不知道情况下，给你安装了一个证书，他随时都可能进入你的系统
任何一个有权限的用户都能很方便的植入一个证书到 .ssh/authorized_keys 文件中
			PubkeyAuthentication no
AuthorizedKeysFile /dev/null			
1.4. 使用证书替代密码认证
是不是自相矛盾？ 这个跟上面讲的正好相反，这里只允许使用key文件登陆。
			PasswordAuthentication no			
这种方式比起密码要安全的多，唯一要注意的地方就是证书被拷贝 ，建议你给证书加上 passphrase。
证书的 passphrase 是可以通过openssl工具将其剥离的，SSH证书我没有试过，但是原理都差不多。
1.5. 图形窗口客户端记忆密码的问题
当你使用XShell, Xftp, WinSCP, SecureCRT, SecureFX ......等等软件登录时，该软件都提供记住密码的功能，使你下次再登陆的时候无须输入密码就可以进入系统。这样做的确非常方便，
但是你是否想过你的电脑一旦丢失或者被其他人进入，那有多么危险。我之前每天背着笔记本电脑上班，上面安装着XShell并且密码全部记忆在里面。这使我意识到一点电脑丢失，有多么可怕。
禁止SSH客户端记住密码，你不要要求别人那么做。你也无法控制，最终我找到了一种解决方案。
			ChallengeResponseAuthentication yes			
每次登陆都回提示你输入密码。密码保存也无效。
1.6. 关闭 GSSAPI
			GSSAPIAuthentication no
#GSSAPIAuthentication yes
#GSSAPICleanupCredentials yes
#GSSAPICleanupCredentials yes
#GSSAPIStrictAcceptorCheck yes
#GSSAPIKeyExchange no			
1.7. 禁止SSH端口映射
禁止使用SSH映射Socks5翻墙等等
			AllowTcpForwarding no			
1.8. IP地址限制
只允许通过192.168.2.1,192.168.2.2 访问本机
# vim /etc/hosts.allow
sshd:192.168.2.1,192.168.2.2			
禁止所有人访问本机
# vim /etc/hosts.deny
sshd:ALL			
上面使白名单策略，你也可以采用黑名单策略。
2. Shell 安全
2.1. .history 文件
SA的操作记录问题
通过~/.bash_history文件记录系统管理员的操作记录，定制.bash_history格式
HISTSIZE=1000
HISTFILESIZE=2000
HISTTIMEFORMAT="%Y-%m-%d-%H:%M:%S "
export HISTTIMEFORMAT			
看看实际效果
$ history | head
    1  2012-02-27-09:10:45 do-release-upgrade
    2  2012-02-27-09:10:45 vim /etc/network/interfaces
    3  2012-02-27-09:10:45 vi /etc/network/interfaces
    4  2012-02-27-09:10:45 ping www.163.com			
2.2. sudo 安全问题
/etc/sudoers
			Cmnd_Alias WEBMASTER = /srv/nginx/sbin/nginx, /srv/php/sbin/php-fpm, !/srv/mysql/bin/*
www localhost = NETWORKING, SERVICES, DELEGATING, PROCESSES, WEBMASTER

Cmnd_Alias Database = /usr/bin/mysqldump, /srv/mysql/bin/mysql, /u01/oracle/10.x.x/bin/sqlplus
mysql localhost = NETWORKING, SERVICES, DELEGATING, PROCESSES, WEBMASTER, Database			
使用www用户测试登录，无误后修改SSH配置文件，禁止root登录。
			vim /etc/ssh/sshd_config
PermitRootLogin no			
然后在测试从www sudo 执行命令, 可能成功启动nginx 与 php-fpm
2.3. 临时文件安全
临时文件不应该有执行权限
/tmp
/dev/sda3 /tmp ext4 nosuid，noexec，nodev，rw 0 0			
同时使用符号连接将/var/tmp 指向 /tmp
/dev/shm
none /dev/shm tmpfs defaults，nosuid，noexec，rw 0 0			
2.4. 执行权限
以数据库为例,从安全角度考虑我们需要如下更改
chown mysql:mysql /usr/bin/mysql*
chmod 700 /usr/bin/mysql*			
mysql用户是DBA专用用户, 其他用户将不能执行mysql等命令。
3. 防火墙
开启防火墙
lokkit --enabled		
3.1. 策略
默认INPUT，FORWARD，OUTPUT 三个都是ACCEPT
			-P INPUT ACCEPT
-P FORWARD ACCEPT
-P OUTPUT ACCEPT			
从安全的角度出发，INPUT，FORWARD，OUTPUT 三个都是DROP最安全，但配置的时候会给你带来非常多的不可预料的麻烦。
			-P INPUT DROP
-P FORWARD DROP
-P OUTPUT DROP			
折中的方案，也是打多少硬件防火墙厂商所采用的方案，他们都是采用INPUT默认禁用所有，OUTPUT默认允许所有，你只要关注INPUT规则即可。
			-P INPUT DROP
-P FORWARD ACCEPT
-P OUTPUT ACCEPT			
3.2. 防止成为跳板机
封锁22等端口，避免相互跳转
iptables -A OUTPUT -p tcp -m multiport --dports 22,21,873 -j REJECT
/etc/init.d/iptables save
iptables -L -n			
web 服务器禁止使用ssh，作为跳板机
用户将不能使用ssh命令登陆到其他电脑
4. Linux 系统资源调配
4.1. /etc/security/limits.conf
很多资料上是这么写的
* soft nofile 65535
* hard nofile 65535 			
这样做是偷懒，会带来很多问题，如果你的服务器被攻击，由于你的设置，系统将耗光你的资源，直到没有任何响应为止，你可能键盘输入都成问题，你不得不重启服务器，但你会发现重启只能维持短暂几分钟，又会陷入无响应状态。
			nobody soft nofile 4096
nobody hard nofile 8192			
为什么会设置为nobody用户呢？因为root用户启动系统后web 服务器会使用nobody用户创建子进程，socket连接实际上是nobody用户在处理。root 仅仅是守护父进程。
mysql soft nofile 2048
mysql hard nofile 2048			
针对 mysql 做限制
提示
关于 nofile 即打开文件数，这个跟socket有非常紧密的关系，在linux系统中任何设备都被看做是一个文件（字符设备），你连接一个鼠标，键盘，摄像头，硬盘等等都被看作打开一个设备文件，所以默认1024是远远不够的。
4.2. 关闭写磁盘I/O功能
对于某些文件没必要记录文件的访问时间，由其是在高并发的IO密集操作的环境下，通过两个参数可以实现noatime,nodiratime减少不必要的系统IO资源。
编辑/etc/fstab 添加 noatime,nodiratime 参数
/dev/sdb1    /www          ext4    noatime,nodiratime        0 0			
5. PAM 插件认证加固配置
配置文件
ls  /etc/pam.d/
chfn         crond                login    passwd            remote    runuser-l          smtp          ssh-keycat  sudo-i       system-auth-ac
chsh         fingerprint-auth     newrole  password-auth     run_init  smartcard-auth     smtp.postfix  su          su-l
config-util  fingerprint-auth-ac  other    password-auth-ac  runuser   smartcard-auth-ac  sshd          sudo        system-auth		
认证插件
ls /lib64/security/		
5.1. pam_tally2.so
此模块的功能是，登陆错误输入密码3次，5分钟后自动解禁，在未解禁期间输入正确密码也无法登陆。
在配置文件 /etc/pam.d/sshd 顶端加入
auth required pam_tally2.so deny=3 onerr=fail unlock_time=300			
查看失败次数
# pam_tally2
Login           Failures Latest failure     From
root               14    07/12/13 15:44:37  192.168.6.2
neo                 8    07/12/13 15:45:36  192.168.6.2			
重置计数器
# pam_tally2 -r -u root
Login           Failures Latest failure     From
root               14    07/12/13 15:44:37  192.168.6.2

# pam_tally2 -r -u neo
Login           Failures Latest failure     From
neo                 8    07/12/13 15:45:36  192.168.6.2			
pam_tally2 计数器日志保存在 /var/log/tallylog 注意，这是二进制格式的文件
例 1. /etc/pam.d/sshd - pam_tally2.so
# cat  /etc/pam.d/sshd
#%PAM-1.0
auth required pam_tally2.so deny=3 onerr=fail unlock_time=300

auth	   required	pam_sepermit.so
auth       include      password-auth
account    required     pam_nologin.so
account    include      password-auth
password   include      password-auth
# pam_selinux.so close should be the first session rule
session    required     pam_selinux.so close
session    required     pam_loginuid.so
# pam_selinux.so open should only be followed by sessions to be executed in the user context
session    required     pam_selinux.so open env_params
session    optional     pam_keyinit.so force revoke
session    include      password-auth				
以上配置root用户不受限制, 如果需要限制root用户，参考下面
auth required pam_tally2.so deny=3 unlock_time=5 even_deny_root root_unlock_time=1800			
5.2. pam_listfile.so
用户登陆限制
将下面一行添加到 /etc/pam.d/sshd 中，这里采用白名单方式，你也可以采用黑名单方式
auth       required     pam_listfile.so item=user sense=allow file=/etc/ssh/whitelist onerr=fail			
将允许登陆的用户添加到 /etc/ssh/whitelist，除此之外的用户将不能通过ssh登陆到你的系统
# cat /etc/ssh/whitelist
neo
www			
例 2. /etc/pam.d/sshd - pam_listfile.so
# cat /etc/pam.d/sshd
#%PAM-1.0
auth       required     pam_listfile.so item=user sense=allow file=/etc/ssh/whitelist onerr=fail
auth       required     pam_tally2.so deny=3 onerr=fail unlock_time=300

auth	   required	pam_sepermit.so
auth       include      password-auth
account    required     pam_nologin.so
account    include      password-auth
password   include      password-auth
# pam_selinux.so close should be the first session rule
session    required     pam_selinux.so close
session    required     pam_loginuid.so
# pam_selinux.so open should only be followed by sessions to be executed in the user context
session    required     pam_selinux.so open env_params
session    optional     pam_keyinit.so force revoke
session    include      password-auth				
sense=allow	白名单方式, sense=deny	黑名单方式
auth       required     pam_listfile.so item=user sense=deny file=/etc/ssh/blacklist onerr=fail			
更多细节请查看手册 $ man pam_listfile
5.3. pam_access.so
编辑 /etc/pam.d/sshd 文件，加入下面一行
account required pam_access.so			
保存后重启sshd进程
编辑 /etc/security/access.conf 文件
			cat >>  /etc/security/access.conf << EOF

- : root : ALL EXCEPT 192.168.6.1
EOF			
只能通过 192.168.6.1 登陆, 添加多个IP地址
- : root : ALL EXCEPT 192.168.6.1 192.168.6.2			
测试是否生效
5.4. pam_wheel.so
限制普通用户通过su命令提升权限至root. 只有属于wheel组的用户允许通过su切换到root用户
编辑 /etc/pam.d/su 文件，去掉下面的注释
auth		required	pam_wheel.so use_uid			
修改用户组别，添加到wheel组
# usermod -G wheel www

# id www
uid=501(www) gid=501(www) groups=501(www),10(wheel)			
没有加入到wheel组的用户使用su时会提示密码不正确。
$ su - root
Password:
su: incorrect password			
```

