```bash
squid代理加用户认证

用authentication helpers添加身份验证

有如下几种认证方式 ：
=> NCSA: Uses an NCSA-style username and password file.
=> LDAP: Uses the Lightweight Directory Access Protocol
=> MSNT: Uses a Windows NT authentication domain.
=> PAM: Uses the Linux Pluggable Authentication Modules scheme.
=> SMB: Uses a SMB server like Windows NT or Samba.
=> getpwam: Uses the old-fashioned Unix password file.
=> SASL: Uses SALS libraries.
=> NTLM, Negotiate and Digest authentication

配置NCSA 认证


一、创建认证用户名/密码，用htpasswd

#htpasswd -c /etc/squid/passwd user1
输入密码
New password:
Re-type new password:

二、确定squid是否支持authentication helper

yum 安装的

#rpm -ql squid | grep ncsa_auth
输出：
/usr/lib64/squid/ncsa_auth


三、配置SQUID认证
# vi /etc/squid/squid.conf

加入验证部分内容：
auth_param basic program /usr/lib64/squid/basic_ncsa_auth /etc/squid/passwd     //定义squid密码文件与ncsa_auth文件位置
auth_param basic children 15          //认证进程的数量
auth_param basic realm Squid proxy-caching web server
auth_param basic credentialsttl 2 hours        //认证有效期
auth_param basic casesensitive off             //用户名不区分大小写，可改为ON区分大小写
添加 acl 验证用户：

acl ncsa_users proxy_auth REQUIRED
http_access allow ncsa_users
四、重启squid生效
# /etc/init.d/squid restart


其他配置：

开启添加磁盘缓存目录：
cache_dir ufs /var/spool/squid 100 16 256
coredump_dir /var/spool/squid

配置dns域名服务器：
dns_nameservers 8.8.8.8 144.144.144.144
```

