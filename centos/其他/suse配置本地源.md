```shell
查看Linux的版本
linux:~ # cat /etc/issue

Welcome to SUSE Linux Enterprise Server 11SP1  (x86_64) - Kernel \r (\l).

查看Linux的内核
linux:~ # cat /proc/version

Linux version 2.6.32.12-0.7-default(geeko@buildhost) (gcc version 4.3.4 [gcc-4_3-branch revision 152973] (SUSELinux) ) #1 SMP 2010-05-20 11:14:20 +0200

挂载ISO镜像文件

mount -o loop -t iso9660  <iso file>  /mnt/iso

 注：查看CDROM设备名称的方法，一般为/dev/cdrom：

*  执行：$ dmesg |egrep -i --color 'cdrom|dvd|cd/rw|writer'

挂载iso文件
mount -0 loop SLES-11-SP3-DVD-x86 64-GM-DVD1.iso  /or

或者拷贝iso目录文件到or
cp -rf /media/SLES-11-SP4-DVD-x86_ 6412211/* /or

配置本地repo
zypper ar file:///or/ local-sles

查看列出库.
zypper Ir

#| Alias| Name| Enabled | Refresh
1 | local-sles | local-sled |Yes| No

清楚本地缓存
zypper clean

刷新所有安装源
zypper ref

列出所有可用的模式
zypper pt

列出所有可用的产品
zypper pd

删除源
zypper rr local-sles

查看安装软件包
zypper se

列出仓库优先级
zypper Ir -p

列出仓库的URI
zypper Ir -u


测试安装
zypper install gcc

使用命令 zypper modifyrepo -d [URL或者Alias] 禁用一个软件源，如：
zypper modifyrepo -d Packman

使用命令 zypper removerepo [URL或者Alias] 删除一个软件源：
zypper removerepo http://packman.inode.at/suse/openSUSE_Leap_42.2/

列出配置的软件源，显示详情（优先级、网址等等）：
zypper repos -d

```

