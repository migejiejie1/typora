```shell
Linux新建用户配置文件 /etc/login.defs 详解

/etc/login.defs 是设置用户帐号限制的文件。该文件里的配置对root用户无效。/etc/login.defs 文件用于在Linux创建用户时，对用户的一些基本属性做默认设置，例如指定用户 UID 和 GID 的范围，用户的过期时间，密码的最大长度，等等。

需要注意的是，该文件的用户默认配置对 root 用户无效。并且，当此文件中的配置与 /etc/passwd 和 /etc/shadow 文件中的用户信息有冲突时，系统会以/etc/passwd 和 /etc/shadow 为准。

如果/etc/shadow文件里有相同的选项，则以/etc/shadow里的设置为准，也就是说/etc/shadow的配置优先级高于/etc/login.defs

==========================================
 /etc/login.defs文件内容

设置项	                                      含义
MAIL_DIR /var/spool/mail 	创建用户时，系统会在目录 /var/spool/mail 中创建一个用户邮箱，比如 lamp 用户的邮箱是 /var/spool/mail/lamp。
PASS_MAX_DAYS 99999	密码有效期，99999 是自 1970 年 1 月 1 日起密码有效的天数，相当于 273 年，可理解为密码始终有效。
PASS_MIN_DAYS 0	表示自上次修改密码以来，最少隔多少天后用户才能再次修改密码，默认值是 0。
PASS_MIN_LEN 5	指定密码的最小长度，默认不小于 5 位，但是现在用户登录时验证已经被 PAM 模块取代，所以这个选项并不生效。
PASS_WARN_AGE 7	指定在密码到期前多少天，系统就开始通过用户密码即将到期，默认为 7 天。
UID_MIN 500 	指定最小 UID 为 500，也就是说，添加用户时，默认 UID 从 500 开始。注意，如果手工指定了一个用户的 UID 是 550，那么下一个创建的用户的 UID 就会从 551 开始，哪怕 500~549 之间的 UID 没有使用。
UID_MAX 60000	指定用户最大的 UID 为 60000。
GID_MIN 500	指定最小 GID 为 500，也就是在添加组时，组的 GID 从 500 开始。
GID_MAX 60000	用户 GID 最大为 60000。
CREATE_HOME yes	指定在创建用户时，是否同时创建用户主目录，yes 表示创建，no 则不创建，默认是 yes。
UMASK 077	用户主目录的权限默认设置为 077。
USERGROUPS_ENAB yes	指定删除用户的时候是否同时删除用户组，准备地说，这里指的是删除用户的初始组，此项的默认值为 yes。
ENCRYPT_METHOD SHA512	指定用户密码采用的加密规则，默认采用 SHA512，这是新的密码加密模式，原先的 Linux 只能用 DES 或 MD5 加密。



==========================================

MAIL_DIR        /var/mail #创建用户时，要在目录/var/spool/mail中创建一个用户mail文件；
FAILLOG_ENAB  yes #登录错误记录到日志
LOG_UNKFAIL_ENAB no #在建立用户时是否创建邮箱
SYSLOG_SU_ENAB  yes #当限定超级用户管理日志时使用。
SYSLOG_SG_ENAB  yes #当限定超级用户组管理日志时使用
SU_NAME  su #使用户在使用su命令后用ps查看相关进程时显示的是-su而不是-sh。
HUSHLOGIN_FILE .hushlogin #打开该选项可以掩盖登录信息,也就是在login(登录)过程中不显示/etc/motd中的信息。
TTYGROUP tty
TTYPERM  0600  #设置用户登陆时的tty权限。
PASS_MAX_DAYS 99999 #用户的密码不过期最多的天数；
PASS_MIN_DAYS 0 #密码修改之间最小的天数；
PASS_WARN_AGE 7 #密码失效前多少天在用户登录时通知用户修改密码

```

