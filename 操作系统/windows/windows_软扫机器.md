```shell
windows软扫机器 挂载166.69/76/77 挂NAS

预备条件：
1、存储配置NAS,samba协议；
外观软扫samba路径:  \\www.gwssi.com.cn\wgznsc01   等于NFS:  www.gwssi.com.cn:/mnt/WGZNSC
samba账号: wgznsc/81348134
（  其它nas卷的samba密码在  中间件表中查找）

2、NAS上创建目录:---（在linux客户端上nfs挂载NAS后mkdir。chmod 777权限)
weblogic中件间:  /app/bea/bea121/user_projects/domains/base_domain/bin/startWebLogic.sh # 修改 umask为 000
[root@nw_wgzns1 ~]# ll /wgznsc/ -d
drwxrwxrwx 5 root root 4096 12月 20 17:49 /wgznsc/
[root@nw_wgzns1 ~]# ll /wgznsc/zwgscan/
总用量 16
drwxrwxrwx 2 weblogic bea 4096 12月 21 11:33 result
drwxrwxrwx 2 weblogic bea 4096 12月 21 14:20 wgzscaisao16669
drwxrwxrwx 2 weblogic bea 4096 12月 21 13:23 wgzssoftprint76
drwxrwxrwx 2 weblogic bea 4096 12月 20 17:52 wgzssoftprint77

10.50.166.69 
r盘： /wgznsc/zwgscan/result
z盘：/wgznsc/zwgscan/wgzscaisao16669
10.50.166.76
r盘： /wgznsc/zwgscan/result
z盘：/wgznsc/zwgscan/wgzssoftprint76
10.50.166.77
r盘： /wgznsc/zwgscan/result
z盘：/wgznsc/zwgscan/wgzssoftprint77

=================================================================================
windows 软扫机器配置项------------------------------------
配置dns10.50.167.201/202
1、挂载R盘Z盘
开始-启动- install.bat
net use \\www.gwssi.com.cn\wgznsc01 81348134 /user:wgznsc
set inputpath=\\www.gwssi.com.cn\wgznsc01\zwgscan\%computername%
set resultpath=\\www.gwssi.com.cn\wgznsc01\zwgscan\result
net use z: /delete
net use r: /delete
subst z: %inputpath%
subst r: %resultpath%

\\172.21.3.145
cepctsan01
Huawei12#$

2、修改操作系统密码  

3、修改注册表，虚拟机输入regedit自动打开的位置，修改包含主机名和密码的地方，保证开机不输密码自动进系统
注册表/HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\；
主机名 键 AltDefaultDomainName 值 WGZSSOFTPRINT76
自动登录  defaultpassword

"AltDefaultDomainName"="SOFTPRINT221"
"DefaultDomainName"="SOFTPRINT221"
"DefaultPassword"=""

4、主机名

5、修改计划任务的主机名和密码  控制面板(control)-任务计划-右键restartComputer任务-设置密码 ；schtasks /query #查看任务。

SOFTPRINT199\Administrator

--------------------------------------------------------------------------------
 删除使用的共享，删除(断开的)网络驱动器： net use \\10.75.161.73\admin$   /d  /y
删除提供的共享，net share  admin$ /d
subst  #查看已映射的驱动器              subst r: /d
control userpasswords2  删除保存的共享用户名密码
nbtstat -A 10.50.166.76
net share #显示本地计算机上所有共享资源的信息
net use  #可查看，正保持共享的网络连接。
net view \\ip 查看对方局域网内开启了哪些共享



-----------------------------------
'10.51.14.16\cepcttemp\Scan' 上的 result
'10.51.14.16\cepcttemp\Scan' 上的 softprint21

服务启停脚本
C:Debug\remoxService.exe

外网软扫跳板机 
10.76.3.56 
Administrator/123qwe!@#QWE

内网 / 外网
10.51.14.16 / 172.16.71.3 
JIUXIA0QIA02\cepctrs
cepctrsgwssi123

\\10.51.14.16\cepcttemp\SCAN\softprint34
```

