``````basic
ossfs能让您在Linux系统中，将对象存储OSS的存储空间（Bucket）挂载到本地文件系统中，您能够像操作本地文件一样操作OSS的对象（Object），实现数据的共享。

不适合高并发读写的场景。
编辑OSS内文件会导致文件被重新上传。
元数据操作（例如list directory）需要远程访问OSS服务器，所以性能较差。
重命名文件或文件夹可能会出错。若操作失败，可能会导致OSS和本地数据不一致。

运行环境
建议您在以下环境中运行ossfs：

Linux系统:
    CentOS 7.0及以上版本
    Ubuntu 14.04及以上版本
fuse 2.8.4以上版本

centos安装: 

1. 下载安装包。
wget http://gosspublic.alicdn.com/ossfs/ossfs_1.80.6_centos7.0_x86_64.rpm
2. 安装ossfs。
sudo yum install ossfs_1.80.6_centos7.0_x86_64.rpm

对于使用yum安装rpm包的客户端，如果客户端节点网络环境特殊，无法直接使用yum下载依赖包。您可以在网络正常的、相同版本操作系统的节点上，使用yum下载依赖包并拷贝到网络特殊的节点。例如，ossfs需要依赖fuse 2.8.4以上版本，可使用如下命令，下载yum源中最新的fuse到本地：
sudo yum install --downloadonly --downloaddir=./ fuse

3. 配置账号访问信息。
将Bucket名称以及具有该Bucket访问权限的AccessKey ID和AccessKey Secret信息存放在/etc/passwd-ossfs文件中。文件的权限建议设置为640。
echo BucketName:yourAccessKeyId:yourAccessKeySecret > /etc/passwd-ossfs
chmod 640 /etc/passwd-ossfs

4. 将Bucket挂载到指定目录。
ossfs BucketName mountfolder -o url=Endpoint

例: 
将杭州地域名称为bucket-test的Bucket挂载到/tmp/ossfs目录下。
echo bucket-test:LTAIbZcdVCmQ****:MOk8x0y9hxQ31coh7A5e2MZEUz**** > /etc/passwd-ossfs
chmod 640 /etc/passwd-ossfs
mkdir /tmp/ossfs
ossfs bucket-test /tmp/ossfs -o url=http://oss-cn-hangzhou.aliyuncs.com

5. 如果您不希望继续挂载此Bucket，您可以将其卸载。
fusermount -u /tmp/ossfs

高级配置
1. 配置账号信息

通过ossfs访问OSS存储空间时，需要配置账号信息，也就是AccessKeyID和AccessKeySecret。这些账号信息需要按照特定的格式写到账号配置文件中。当挂载ossfs时，会从这个账号配置文件上获取账号信息，格式为$bucket_name:$access_key_id:$access_key_secret。
账号配置文件的默认路径为/etc/passwd-ossfs，您也可以通过 -opasswd_file=passwd-path 选项指定配置文件。两者的区别在于：默认路径的权限可以是640，其他路径下的配置文件权限必须是600。

1) 同一个账号配置文件里可以保存多条账号信息，一条记录一行。ossfs会根据挂载的存储空间名称匹配到正确的账号上。
2) 当需要同时挂载多个存储空间时，您可以将所有的配置信息写到同一个账号配置文件里，也可以将不同的账号信息写到不同的账号配置文件中，通过 -opasswd_file=xxx 选项加载。

2. 使用实例RAM角色访问
在云服务器ECS上，您还可以通过实例RAM角色的方式挂载ossfs。实例RAM角色允许您将一个角色关联到云服务器实例，在实例内部基于临时凭证STS访问OSS。临时凭证由系统自动生成和更新，应用程序可以使用指定的实例元数据URL获取临时凭证，无需特别管理。借助于RAM，一方面保证AccessKey安全，另一方面实现权限的精细化控制和管理。

挂载ossfs，并增加-oram_role选项
ossfs bucket1 /tmp/ossfs -ourl=http://oss-cn-hangzhou.aliyuncs.com -oram_role=http://100.100.100.200/latest/meta-data/ram/security-credentials/EcsRamRoleOssTest

3. 配置访问权限
ossfs挂载的目录访问权限默认为挂载点的所有者，即执行挂载命令的用户，其他用户无法访问。如果要修改默认的权限设置，例如允许其他用户或用户组访问挂载点，可以在运行ossfs的时候使用如下参数，做到期望的权限设置。
allow_other：赋予计算机上其他用户访问挂载目录的权限，但不包括目录内的文件。如果您要更改文件夹中的文件访问权限，请用chmod命令。该选项不需要设置选项值，如果需要启用，请直接添加-oallow_other选项。
uid：设置文件夹属于某个用户时填写的用户uid。
gid：设置文件夹属于某个用户时填写的用户gid。
mp_umask：用来设定挂载点的权限掩码，只有当allow_other选项设置后，该选项才生效，默认值为000。使用方法与umask命令使用方式一致。例如需要设置挂载点的权限为770，则增加-oallow_other -omp_umask=007；需要设置挂载点的权限为700，则增加-oallow_other -omp_umask=077。

配置示例：
1) 允许所有用户访问，即权限为777。
ossfs bucket_name mount_point -ourl=endpoint -oallow_other
2) 只允许同组用户访问，即权限为770。
ossfs bucket_name mount_point -ourl=endpoint -oallow_other -omp_umask=007
3) 挂载时指定为其他用户和组，同时只允许同组的用户访问，即权限为770。
以www用户为例说明，先通过id命令获取用户的uid和gid信息，之后在挂载时指定uid和gid参数。

id www
uid=1000(www) gid=1000(web) groups=1000(web)
ossfs bucket_name mount_point -ourl=endpoint -oallow_other -ouid=1000 -ogid=1000 -omp_umask=007

4. 挂载指定文件目录
ossfs除了可以把整个存储空间挂载到本地文件系统外，还可以通过设置前缀，把存储空间下的某个文件目录挂载到本地文件系统。命令格式如下：
ossfs bucket:/prefix mount_point -ourl=endpoint
通过这个方式挂载时，需要确保存储空间里存在${prefix}/ 这样一个对象。可以通过ossutil的stat命令查询该对象是否存在。

示例：将位于杭州地域的存储空间bucket-ossfs-test下的folder目录挂载到/tmp/ossfs-folder下。
ossfs bucket-ossfs-test:/folder /tmp/ossfs-folder -ourl=http://oss-cn-hangzhou.aliyuncs.com

5. 开机自动挂载目录

1) 将Bucket名称、AccessKeyID、AccessKeySecret等信息写入/etc/passwd-ossfs文件，并将文件权限修改为640。
2) 设置开机自动挂载。
CentOS 7.0及以上的系统通过开机自动启动脚本进行挂载
a. 在/etc/init.d/目录下建立文件ossfs，将模板文件中的内容拷贝到这个新文件中。并将其中的your_xxx内容改成您自己的信息。
模板文件: 
#! /bin/bash
#
# ossfs      Automount Aliyun OSS Bucket in the specified direcotry.
#
# chkconfig: 2345 90 10
# description: Activates/Deactivates ossfs configured to start at boot time.

ossfs your_bucket your_mountpoint -ourl=your_url -oallow_other

b. 为新建立的ossfs脚本赋予可执行权限：
chmod a+x /etc/init.d/ossfs
命令执行完成后，您可以尝试执行该脚本，如果脚本文件内容无误，那么此时OSS中的Bucket已经挂载到您指定的目录下了。
c. 把ossfs启动脚本作为其他服务，开机自动启动：
chkconfig ossfs on
d. 执行上述步骤后，ossfs就可以开机自动挂载了。

6. 使用Supervisor启动ossfs
Supervisor是用Python开发的一套通用的进程管理程序，能将一个普通的命令行进程变为后台daemon，并监控进程状态。异常退出时能自动重启。使用Supervisor启动ossfs的步骤如下：

1) 安装supervisor。
yum install supervisor
2) 创建ossfs的启动脚本。
a. 创建start_ossfs.sh文件。
mkdir /root/ossfs_scripts
vi /root/ossfs_scripts/start_ossfs.sh
b. 写入启动脚本。
脚本文件内容:
# 卸载
fusermount -u /mnt/ossfs
# 重新挂载，必须要增加-f参数运行ossfs，让ossfs在前台运行。
exec ossfs bucket_name mount_point -ourl=endpoint -f

3) 编辑/etc/supervisor/supervisord.conf文件，在最后加入如下内容：
[program:ossfs]
command=bash /root/ossfs_scripts/start_ossfs.sh
logfile=/var/log/ossfs.log
log_stdout=true
log_stderr=true
logfile_maxbytes=1MB
logfile_backups=10
4) 运行Supervisor。
supervisord
5) 确认运行正常。
ps aux | grep supervisor # 应该能看到Supervisor进程。
ps aux | grep ossfs # 应该能看到ossfs进程。
kill -9 ossfs # 关闭ossfs进程，Supervisor应该会重启它。不要使用killall，因为killall发送SIGTERM，进程正常退出，Supervisor不再去重新运行ossfs。
ps aux | grep ossfs # 应该能看到ossfs进程。

7. 开启调试日志
在使用ossfs的过程中，可能会遇到一些问题。这个时候需要开启调试日志，通过日志信息分析和定位问题。您可以通过如下方式开启调试日志：
在挂载目录时增加-d -odbglevel=debug -ocurldbg选项，ossfs会把日志写入系统日志中。
CentOS系统
日志保存在/var/log/messages中。

在挂载目录时使用-d -odbglevel=debug -ocurldbg -f选项，ossfs会把日志输出到屏幕上。
``````

