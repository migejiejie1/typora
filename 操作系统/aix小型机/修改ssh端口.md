**aix小型机修改sshd远程连接端口**

```shell
允许root用户通过telnet服务登录
cp /etc/security/user{,.old}
vi /etc/security/user
	root:
		rlogin = false -> true

开启telnet服务
1) 保证instd守护进程是开启的
bash-4.2# lssrc -s inetd
Subsystem         Group            PID          Status 
 inetd            tcpip            1639148      active

active表示inetd进程已开启

2) 启动telnet服务
bash-4.2# startsrc -t telnet 
0513-124 The telnet subserver has been started.

3) 查看telnet服务状态
bash-4.2# lssrc -t telnet 
Service       Command                  Description              Status 
 telnet       /usr/sbin/telnetd        telnetd -a               active
active表示telnet进程已开启

4) 查看telnet端口是否正常监听
bash-4.2# netstat -an |grep 23     
tcp        0      0  *.23                   *.*                    LISTEN

5) 使用telnet远程登录服务器
telnet 10.50.166.30
login: root
root's Password: xxx

6) 关闭telnet服务
bash-4.2# stopsrc -t telnet 
0513-127 The telnet subserver was stopped successfully.

bash-4.2# lssrc -t telnet
Service       Command                  Description              Status 
 telnet                                                         inoperative 

7) 重启sshd服务,先关闭,在启动 
关闭
bash-4.2# stopsrc -s sshd
0513-044 The sshd Subsystem was requested to stop.
bash-4.2# lssrc -s sshd
Subsystem         Group            PID          Status 
 sshd             ssh                           inoperative
 
启动
bash-4.2# startsrc -s sshd
0513-059 The sshd Subsystem has been started. Subsystem PID is 3080798.
bash-4.2# lssrc -t telnet
Service       Command                  Description              Status 
 telnet       /usr/sbin/telnetd        telnetd -a               active

=================================
aix修改ssh远程连接端口

cd  /etc/ssh 
grep Port sshd_config

echo Port 12306 >> sshd_config
grep Port sshd_config

stopsrc -s sshd
lssrc -s sshd

startsrc -s sshd
lssrc -s sshd

netstat -an |grep 12306

```

