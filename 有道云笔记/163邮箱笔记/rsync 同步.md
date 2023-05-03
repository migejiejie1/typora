```shell
应用场景
rsync是可以实现增量备份的工具。配合任务计划，rsync能实现定时或间隔同步，配合inotify或sersync，可以实现触发式的实时同步。
同步过程
rsync同步过程中由两部分模式组成：决定哪些文件需要同步的检查模式以及文件同步时的同步模式。
检查模式
指按照指定规则来检查哪些文件需要被同步，例如哪些文件是明确被排除不传输的。默认情况下，rsync使用"quick check"算法快速检查源文件和目标文件的大小、
mtime(修改时间)是否一致，如果不一致则需要传输。当然，也可以通过在rsync命令行中指定某些选项来改变quick check的检查模式，比如"--size-only"选项表示"quick check"
将仅检查文件大小不同的文件作为待传输文件。rsync支持非常多的选项，其中检查模式的自定义性是非常有弹性的。
同步模式
指在文件确定要被同步后，在同步过程发生之前要做哪些额外工作。例如上文所说的是否要先删除源主机上没有但目标主机上有的文件，是否要先备份已存在的目标文件，
是否要追踪链接文件等额外操作。rsync也提供非常多的选项使得同步模式变得更具弹性。
相对来说，为rsync手动指定同步模式的选项更常见一些，只有在有特殊需求时才指定检查模式，因为大多数检查模式选项都可能会影响rsync的性能。
三种工作模式
本地文件系统上实现同步：
　　rsync -auv /opt/rsync/ /opt/local/
本地主机使用远程shell和远程主机通信(scp方式)
　　rsync -auv /opt/rsync/ root@192.168.1.101:/opt/test
本地主机通过网络套接字连接远程主机上的rsync daemon
　　rsync -auv /opt/rsync test@192.168.1.101::test

说明：
1.前面的命令为源目录，后者为目的地目录。表示以源目录为基准，把源目录文件同步到目的目录。
2.如果多余2个路径，那么最后一个路径为目标路径，前面都是源目录，表示把所有源目录下文件都同步到目标路径
3.源目录如果不带后缀“/”表示在目标目录下创建该目录并把源目录下文件一并同步过去，带“/”表示只是把源目录下的文件全部同步过去。
4.关于第三种模式，server端可以单独以一个进程方式启动，也可以通过xinetd方式启动，两者都是监听873端口，第三种模式可以指定要对外开放的目录，并对目录上传/下载权限控制对client端通过ip 限制，可以设置密码等等。相比于xinetd 独立进程模式处理请求能力更大，而xinetd 更轻量。
常见用法
-n 　　　　　　　　仅测试传输，而不实际传输。常和"-vvvv"配合使用来查看rsync是如何工作的。
-auv (一般常用此3个参数同步)
　　　　　　　　-v：显示rsync过程中详细信息。可以使用"-vvvv"获取更详细信息
　　　　　　　　-a --archive ：归档模式，表示递归传输并保持文件属性。等同于"-rtopgDl"。
　　　　　　　　-u --update ：仅在源mtime比目标已存在文件的mtime新时才拷贝。注意，该选项是接收端判断的，不会影响删除行为
--exclude 　　　　排除某个目录/文件 rsync -r -v --exclude="anaconda/*.log" /var/log/anaconda /var/log/audit /tmp
--delete　　　　　使用"--delete"选项后，接收端的rsync会先删除目标目录下已经存在，但源端目录不存在的文件。也就是"多则删之，少则补之"。
注：如果将"--delete"选项和"--exclude"选项一起使用，则被排除的文件不会被删除
参数说明
-v：显示rsync过程中详细信息。可以使用"-vvvv"获取更详细信息。-P：显示文件传输的进度信息。(实际上"-P"="--partial --progress"，其中的"--progress"才是显示进度信息的)。-n --dry-run  ：仅测试传输，而不实际传输。常和"-vvvv"配合使用来查看rsync是如何工作的。-a --archive  ：归档模式，表示递归传输并保持文件属性。等同于"-rtopgDl"。-r --recursive：递归到目录中去。-t --times：保持mtime属性。强烈建议任何时候都加上"-t"，否则目标文件mtime会设置为系统时间，导致下次更新          ：检查出mtime不同从而导致增量传输无效。-o --owner：保持owner属性(属主)。-g --group：保持group属性(属组)。-p --perms：保持perms属性(权限，不包括特殊权限)。-D        ：是"--device --specials"选项的组合，即也拷贝设备文件和特殊文件。-l --links：如果文件是软链接文件，则拷贝软链接本身而非软链接所指向的对象。-z        ：传输时进行压缩提高效率。-R --relative：使用相对路径。意味着将命令行中指定的全路径而非路径最尾部的文件名发送给服务端，包括它们的属性。用法见下文示例。--size-only ：默认算法是检查文件大小和mtime不同的文件，使用此选项将只检查文件大小。-u --update ：仅在源mtime比目标已存在文件的mtime新时才拷贝。注意，该选项是接收端判断的，不会影响删除行为。-d --dirs   ：以不递归的方式拷贝目录本身。默认递归时，如果源为"dir1/file1"，则不会拷贝dir1目录，使用该选项将拷贝dir1但不拷贝file1。--max-size  ：限制rsync传输的最大文件大小。可以使用单位后缀，还可以是一个小数值(例如："--max-size=1.5m")--min-size  ：限制rsync传输的最小文件大小。这可以用于禁止传输小文件或那些垃圾文件。--exclude   ：指定排除规则来排除不需要传输的文件。--delete    ：以SRC为主，对DEST进行同步。多则删之，少则补之。注意"--delete"是在接收端执行的，所以它是在            ：exclude/include规则生效之后才执行的。-b --backup ：对目标上已存在的文件做一个备份，备份的文件名后默认使用"~"做后缀。--backup-dir：指定备份文件的保存路径。不指定时默认和待备份文件保存在同一目录下。-e          ：指定所要使用的远程shell程序，默认为ssh。--port      ：连接daemon时使用的端口号，默认为873端口。--password-file：daemon模式时的密码文件，可以从中读取密码实现非交互式。注意，这不是远程shell认证的密码，而是rsync模块认证的密码。-W --whole-file：rsync将不再使用增量传输，而是全量传输。在网络带宽高于磁盘带宽时，该选项比增量传输更高效。--existing  ：要求只更新目标端已存在的文件，目标端还不存在的文件不传输。注意，使用相对路径时如果上层目录不存在也不会传输。--ignore-existing：要求只更新目标端不存在的文件。和"--existing"结合使用有特殊功能，见下文示例。--remove-source-files：要求删除源端已经成功传输的文件。
安装配置（第三种模式）
服务端操作
关闭selinux 和 iptables
yum -y install rsync xinetd
vim /etc/xinetd.d/rsync
service rsync
{
disable = no #把yes 改为no
flags = IPv6
socket_type = stream
wait = no
user = root
server = /usr/bin/rsync
server_args = --daemon
log_on_failure += USERID
}
mkdir /etc/rsyncd/

vi /etc/rsyncd/rsyncd.conf #新建文件
#全局配置区域
uid = root 　　　　　　　　　　　　 #uid必须是系统真实存在的用户，表示使用哪个用户启动这个进程
gid = root
port = 873 　　　　　　　　　　　　 #默认为873，可以更改
read only = no
list = no
use chroot = no
max connections = 4 　　　　　　　   #最大连接数
strict modes = yes 　　　　　　　　   #检查pass文件权限
pid file = /var/run/rsyncd.pid
lock file =/var/run/rsync.lock
log file = /var/log/rsyncd.log
#模块配置区域
#module名字和路径，一个配置文件可以定义多个模块针对多个目录进行同步权限开发限制。
[backup]
path = /usr/local/svndata/
commet = This is backup 　　　　　　 #该模块功能描述
ignore errors 　　　　　　　　　　　   #忽略一些无关的IO错误
read only = no 　　　　　　　　　　    #yes 只能下载不能上传，no 都可以（如果都不限制最好配置成no，而不是都不写此配置）
read only = no 　　　　　　　　　　    #yes 只能上传不能下载，no 都可以
list = false 　　　　　　　　　　　　   #不允许该模块被客户端列出
auth users = test　　　　　　　　　　 # 指定连接到该模块的用户列表，只有列表里的用户才能连接到模块，用户名和对应密码保存在secrts file中，
　　　　　　　　　　　　　　　　　  # 这里使用的不是系统用户，而是虚拟用户。不设置时，默认所有用户都能连接，但使用的是匿名连接
secrets file = /etc/rsyncd/rsync.pass
hosts allow = 192.168.1.102 　　　　   #允许访问ip
hosts deny =0.0.0.0/0 　　　　　　      #拒绝剩余所有

ln -s /etc/rsyncd/rsyncd.conf /etc/rsyncd.conf
echo "test:test123" > /etc/rsyncd/rsyncd.pass
chmod 600 /etc/rsyncd/rsyncd.pass #必须有此步骤
netstat -lnp | grep 873
客户端
yum -y install rsync
mkdir /etc/rsyncd/
echo "test:test123" > /etc/rsyncd/rsyncd.pass
chmod 600 /etc/rsyncd/rsyncd.pass
mkdir -p /opt/rsync
#1.back 为服务端定义的模块名称，实际路径为/usr/bin/rsync
#2.第一个路径为源路径第二个路径为目的路径
rsync -auv --password-file=/etc/rsyncd/rsyncd.pass 192.168.1.101::back /opt/rsync/
注意事项：
1.server端注释auth users 和 screts file 两项时那么客户端无需认证即可同步
2.客户端同步命令， test@192.168.1.1::back test是通过test用户与server认证，若不写默认为root用户
3.需要认证：同步命令不加指定密码文件参数那么是交互式，需要手输
4.需要认证：客户端同步命令可以加参数 --password-file=/etc/rsyncd/rsyncd.pass 变为免交互，注意密码文件只记录密码而服务端的格式：root:password 格式，客户端密码文件格式：password ，且密码文件权限为600
5.客户端推到服务端时，文件的属主和属组是配置文件中指定的uid和gid,若为非root用户要保证同步的目录有相应权限。但是客户端从服务端拉的时候，文件的属主和属组是客户端       正在操作rsync的用户身份，因为执行rsync程序的用户为当前用户。
6.关于secrets file的权限，实际上并非一定是600，只要满足除了运行rsync daemon的用户可读即可。是否检查权限的设定是通过选项strict mode设置的，如果设置为false，则无需关注文件的权限。但默认是yes，即需要设置权限
7.服务端启动可以指定配置文件，也可以指定端口，都不指定默认配置文件为/etc/rsyncd.conf 端口为873。
8、报错rsync: opendir "." (in xxx) failed: Permission denied (13)
     原因：服务端未关闭selinux。解决：关闭selinux 即可。
```

