```shell

glibc2.12升级至2.14

#解压并安装
yum -y install gcc gcc-c++ make 
tar -zxvf glibc-2.14.tar.gz
cd glibc-2.14
mkdir build && cd build
../configure --prefix=/usr/local/glibc-2.14
make -j4
make install 

#修改/lib64/libc.so.6
mv  /lib64/libc.so.6 /lib64/libc.so.6.bak
LD_PRELOAD=/usr/local/glibc-2.14/lib/libc-2.14.so ln -s /usr/local/glibc-2.14/lib/libc-2.14.so /lib64/libc.so.6

#查看是否链接成功

ls -l /lib64/libc.so.6 
ll /lib64/libc**
reboot

# 修改系统字符集编码
vim /etc/sysconfig/i18n 

export LANG=C
export LC_ALL=C

source /etc/sysconfig/i18n
locale

==========================================

升级libstdc++.so.6

cp libstdc++.so.6.0.19 /usr/lib64/
cd /usr/lib64/
rm libstdc++.so.6 
ln -s libstdc++.so.6.0.19 libstdc++.so.6
ls -l libstdc++.so.6 

```

