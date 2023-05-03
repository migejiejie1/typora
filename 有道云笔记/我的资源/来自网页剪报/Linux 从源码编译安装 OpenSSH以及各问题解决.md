简单的说 OpenSSH 是一组安全远程的连接工具，主要包括了几个部份：ssh、sshd、scp、sftp、ssh-keygen、ssh-agent、ssh-add。OpenSSH 安装配置比较复杂，难点在配置，特别是在 VPS 中，配置不当就完全无法链接 VPS 了。

**一、关于 OpenSSH**

OpenSSH 是一组用于安全地访问远程计算机的连接工具。它可以作为 rlogin、 rsh rcp 以及 telnet 的直接替代品使用。更进一步， 其他任何 TCP/IP 连接都可以通过 SSH 安全地进行隧道/转发。 OpenSSH 对所有的传输进行加密， 从而有效地阻止了窃听、 连接劫持，以及其他网络级的攻击。

ssh（SSH 客户端，用于登录建立连接，是 rlogin 与 Telnet的安全替代方案）

sshd （SSH 服务端，典型的独立守护进程）

scp、sftp （文件安全传输工具，rcp、ftp 安全的替代方案）

ssh-keygen （用于产生 RSA 或 DSA 密钥）

ssh-agent、ssh-add（帮助用户不需要每次都要输入金钥密码的工具）

**二、编译前的准备工作**

**2.1、查看 OpenSSH 版本**

部分 Linux 系统已默认安装了 OpenSSH，像 [Ubuntu](http://www.linuxidc.com/topicnews.aspx?tid=2) Server 10.10 就已安装了 OpenSSH_5.5p1

```
ssh-v
```



**2.2、安装 OpenSSL 及编译环境**

必须先安装依赖 OpenSSL，具体见《[Linux 从源码编译安装 OpenSSL](http://www.linuxidc.com/Linux/2011-10/45738.htm)》 http://www.linuxidc.com/Linux/2011-10/45738.htm

**2.3、备份 OpenSSH 旧配置文件**

```
cp /etc/init.d/ssh /etc/init.d/ssh.old 
cp -r /etc/ssh /etc/ssh.old 
cp ~/.ssh/authorized_keys ~/.ssh/authorized_keys.old
```



**2.4、卸载旧版 OpenSSH**

```
apt-get purge openssh-client openssh-server
```



**三、编译安装 OpenSSH**

**3.1、关于特权分离**

所谓特权分离(Privilege Separation)实际上是一种 OpenSSH 的安全机制，该特性默认开启，可通过配置文件中的 UsePrivilegeSeparation 指令开启或关闭。

```
mkdir -p /var/empty #设置一个空目录
chown 0:0 /var/empty #所有者和组，0代表"root"
chmod 000 /var/empty #目录权限设置为"000"
groupadd sshd #建立sshd组
useradd -g sshd -c'sshd privsep'-d/var/empty -s/bin/false sshd #用于特权分离的非特权用户"sshd"
```



**3.2、编译安装 OpenSSH**

详细编译选项见《OpenSSH-4.7p1 安装指南》

下载在 http://www.linuxidc.com/Linux/2011-10/45740.htm

```
wget http://ftp.aso.ee/pub/OpenBSD/OpenSSH/portable/openssh-5.6p1.tar.gz
tar -zxf openssh-5.6p1.tar.gz
cd openssh-5.6p1/
./configure --prefix=/usr/local--sysconfdir=/usr/local/ssh--with-ssl-dir=/usr/local/ssl --with-privsep-path=/var/empty --with-privsep-user=sshd --with-zlib=/usr/local/lib --with-ssl-engine--with-md5-passwords--disable-etc-default-login
make && make install
```



--prefix 安装目录

--sysconfdir 配置文件目录

--with-ssl-dir 指定 OpenSSL 的安装目录

--with-privsep-path 非特权用户的chroot目录

--with-privsep-user=sshd 指定非特权用户为sshd

--with-zlib 指定zlib库的安装目录

--with-md5-passwords 支持读取经过MD5加密的口令

--with-ssl-engine 启用OpenSSL的ENGINE支持

**3.3、开机自启动 sshd**

```
mv /etc/init.d/ssh.old /etc/init.d/sshd #使用原来的启动脚本
vim /etc/init.d/sshd #编辑，然后替换路径。
update-rc.d mysql defaults
```

```
#将原路径"/usr/sbin替换为"/usr/local/sibn"
:%s/usr\/sbin/usr\/local\/sbin/g
#将原路径"/etc/ssh替换为"/usr/local/ssh"
:%s/etc\/ssh/usr\/local\/ssh/g
```



**四、 OpenSSH 安全配置**

**4.1、查看 OpenSSH 配置文件**

```
cd /usr/local/ssh
```



moduli #ssh服务器的Diffie-Hellman密钥文件

ssh_config #ssh客户端配置文件

sshd_config #ssh服务器配置文件

ssh_host_dsa_key #ssh服务器dsa算法私钥

ssh_host_dsa_key.pub #ssh服务器dsa算法公钥

ssh_host_rsa_key #ssh服务器rsa算法私钥

ssh_host_rsa_key.pub #ssh服务器rsa算法公钥

**4.2、生成服务器密钥对**

默认 OpenSSH 安装完毕后就自动生成了，如果丢失可通过下面命令重新生成。

```
ssh-keygen -t rsa1 -f /etc/ssh/ssh_host_key -N''
#适用于ssh-1版
ssh-keygen-t rsa -f/etc/ssh/ssh_host_rsa_key -N''
ssh-keygen-t dsa -f/etc/ssh/ssh_host_dsa_key -N''
chmod 600 /etc/ssh/ssh_host_*
chmod 644 /etc/ssh/ssh_host_*.pub
```



**特别注意：**

1、系统密钥对是不能设置密码的， -N 后面 是两个 单引号 ！表示密码串为空。

2、注意公钥和私钥的权限是不同的。



使用最新openssl 1.0.1h 编译安装 openssh 6.6p1

```shell
openssh从6.5版本开始，使用openssl 源码编译的时候，必须使用动态库（在openssh 6.4之前的版本中没这种情况）；一直没找到具体的说明，但是经过无数次编译尝试，终于验证这种事实。

安装openssl时候加上，使用--shared 参数建立共享库文件，否则在编译openssh过程中会报错；报错类型根据不同的安装习惯，可能有多种
1.) configure: error: *** Can't find recent OpenSSL libcrypto (see config.log for details) ***
2.) OpenSSL version mismatch. 
3.) checking OpenSSL header version... not found


具体安装过程如下：

1. 下载最新软件包源码
http://ftp5.usa.openbsd.org/pub/OpenBSD/OpenSSH/portable/openssh-6.6p1.tar.gz
http://www.openssl.org/source/openssl-1.0.1h.tar.gz
http://www.openssl.org/source/openssl-fips-2.0.5.tar.gz

2. 使用YUM安装必要的软件开发包
# yum install -y zlib-devel pam-devel tcp_wrappers-devel

3. 安装openssl-fips，此为FIPS 140-2 support module for openssl, 具体说明参见http://www.openssl.org/docs/fips 
# tar zxpf openssl-fips-2.0.5.tar.gz
# cd openssl-fips
# ./config
# make && make install

4. 安装OpenSSL
# tar zxpf openssl-1.0.1h.tar.gz
# cd openssl-1.0.1h
# ./config fips --shared
# make && make install 

5. 将新编译的openssl library 加入系统动态库链接中
# echo "/usr/local/ssl/lib" >> /etc/ld.so.conf
# ldconfig
update ：如果在第4步openssl 编译过程中，将其设定为OS默认安装目录（--prefix=/usr），那么此步骤无需执行！


6. 安装OpenSSH
# tar zxpf openssh-6.6p1.tar.gz
# cd openssh-6.6p1
# ./configure --prefix=/usr/local/ssh --sysconfdir=/etc/ssh --with-md5-passwords --with-pam --with-tcp-wrappers --with-ssl-dir=/usr/local/
# make && make install

!!! 另一种处理方案是， 在编译openssh的时候，使用--without-hardening参数。 此动作，将改变openssh编译时候gcc参数，关闭 fPIE。个人认为此方案仅是种解决问题的方法，不是最优。
URL：http://www.gossamer-threads.com/lists/openssh/dev/57830

期待openssh 官方提供根本解决方案。

具体到上述安装步骤的改变为：

步骤4，去掉--shared 参数，等同于使用默认值
# ./config fips 
# make && make install 
步骤5，取消

步骤6，更新如下
# ./configure \
  --prefix=/usr \
  --sysconfdir=/etc/ssh \
  --with-md5-passwords \
  --with-pam \
  --with-tcp-wrappers \
  --with-ssl-dir=/usr/local/ssl \
  --without-hardening
# make && make install 

对最近OpenSSL的频繁爆漏洞，比较无奈。。。
checking OpenSSL header version... not found configure: error: OpenSSL version header not found.问题
系统没有找到openssl库，需要运行ldconfig。
http://www.linuxidc.com/Linux/2011-10/45739.htm
```

