```shell
#!/bin/bash
#升级中标麒麟linux5.x 6.x OpenSSH_8.5p1, OpenSSL 1.0.2r  26 Feb 2019

echo "[step 1/9 : config local repo]"
which lsb_release > /dev/null || yum install redhat-lsb-core -y
OS_VER=`lsb_release -a|sed -n '4p'|awk '{print $2}'`
gzip /etc/yum.repos.d/*
cat << EOF  >> /etc/yum.repos.d/local.repo
[local]
name=local
baseurl=http://10.50.167.58/mirrors/yum/ns/ns$OS_VER/
enabled=1
gpgcheck=0
EOF
yum clean all
yum makecache
echo

echo "[step 2/9 : current openssh version]"
rpm -q zlib
openssl version
ssh -V
echo

# echo "[install telnet-server]"
# yum -y install telnet-server*
# echo "[enable telnet-server]"
# sed -i 's/yes/no/g' /etc/xinetd.d/telnet
# mv /etc/securetty /etc/securetty.bak
# service xinetd restart
# chkconfig xinetd on
# echo

echo "[step 3/9 : install devel tool]"
yum install -y gcc pam-devel zlib-devel
echo

echo "[step 4/9 : download zlib and openssl and openssh source]"
mkdir openssh
cd openssh
wget http://10.50.167.58/mirrors/src/openssh/zlib-1.2.11.tar.gz
wget http://10.50.167.58/mirrors/src/openssh/openssl/openssl-1.0.2r.tar.gz
wget http://10.50.167.58/mirrors/src/openssh/openssh-8.5p1.tar.gz
echo


echo "[step 5/9 : compile and install zlib]"
tar -zxf zlib-1.2.11.tar.gz
cd zlib-1.2.11
./configure --prefix=/usr/local/zlib && make && make install
cd ..
echo

echo "[step 6/9 : compile and install openssl]"
tar -zxf openssl-1.0.2r.tar.gz
cd openssl-1.0.2r/
./config --prefix=/usr/local/openssl && make depend && make && make install
# mv /usr/bin/openssl /usr/bin/openssl.old
# ln -snf /usr/local/openssl/bin/openssl /usr/bin/openssl
cd ..
openssl version -a
echo

echo "[step 7/9 : backup current openssh]"
mv /etc/ssh /etc/ssh.old
mv /etc/pam.d/sshd /etc/pam.d/sshd.old
echo

echo "[step 8/9 : remove current openssh]"
rpm -qa |grep openssh|xargs -i rpm -e --nodeps {}
echo

echo "[step 9/9 : compile and install openssh]"
tar -zxf openssh-8.5p1.tar.gz
cd openssh-8.5p1
# sed -i 's/"OpenSSH_8.5"/" "/g' version.h
# sed -i 's/"p1"/" "/g' version.h
./configure --prefix=/usr/local/openssh --sysconfdir=/etc/ssh --with-ssl-dir=/usr/local/openssl --with-zlib=/usr/local/zlib --with-md5-passwords --with-pam --without-hardening && make && make install
cp contrib/ssh-copy-id /usr/local/openssh/bin/
chmod +x /usr/local/openssh/bin/ssh-copy-id
ln -snf /usr/local/openssh/bin/* /usr/bin/
ln -snf /usr/local/openssh/sbin/sshd /usr/sbin/sshd
ssh -V
echo "X11Forwarding no" >> /etc/ssh/sshd_config
echo "PermitRootLogin yes" >> /etc/ssh/sshd_config
echo "UseDNS no" >> /etc/ssh/sshd_config
cp -p contrib/redhat/sshd.init /etc/init.d/sshd
chmod +x /etc/init.d/sshd
chkconfig --add sshd
chkconfig sshd on
chkconfig --list sshd
#rm -rf /etc/ssh
#mv /etc/ssh.old /etc/ssh
service sshd start
echo

#echo "[remove telnet-server]"
#mv /etc/securetty.bak /etc/securetty
#chkconfig xinetd off
#rpm -qa |grep telnet-server|xargs -i rpm -e --nodeps {}
#service xinetd stop

```

