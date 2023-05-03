```shell
rpm -qa |grep openssl
rpm -qa |grep openssh

 openssl-libs-1.0.1e-42.el7.x86_64
 openssl-1.0.1e-42.el7.x86_64
 openssh-server-6.6.1p1-11.el7.x86_64
 openssh-clients-6.6

yum -y install wget zlib-devel openssl-devel 

wget https://cikeblog.com/e/openssh-8.0p1.tar.gz

systemctl stop sshd
systemctl status  sshd
ss -anpt |grep sshd

vim /usr/local/openssh/etc/sshd_config
ps -ef |grep sshd
ss -anpt |grep sshd


```

