# [KVM之五：KVM日常管理常用命令](https://www.cnblogs.com/chenjiahe/p/5919426.html)

1、查看、编辑及备份KVM 虚拟机配置文件 以及查看KVM 状态：

1.1、KVM 虚拟机默认的配置文件在 /etc/libvirt/qemu 目录下，默认是以虚拟机名称命名的.xml 文件，如下，：

```
1 [root@kvm ~ 11:41:41]#ls /etc/libvirt/qemu/
2 networks  snale2.xml  snale.xml
```

1.2、KVM 虚拟机配置文件的修改。可以使用vi 或 vim 命令进行编辑修改，但不建议。正确的做法为 virsh edit KVM-NAME：

```
1 [root@kvm qemu 11:43:41]#virsh edit snale 
```

1.3、备份KVM 虚拟机配置文件，先创建一个备份目录：

```
[root@kvm ~ 11:46:04]#mkdir /data/kvmback
1 [root@kvm ~ 11:46:04]#virsh dumpxml snale >/data/kvmback/snale_back.xml
```

1.4、正在运行的KVM 虚拟机的状态可以用virsh list 查看：

```
1 [root@kvm ~ 11:48:42]#virsh list
2  Id    名称                         状态
3 ----------------------------------------------------
4  4     snale                          running
```

查看全部的虚拟机状态则在virsh list 后面加参数 --all 即可：

```
1 [root@kvm ~ 11:48:47]#virsh list --all
2  Id    名称                         状态
3 ----------------------------------------------------
4  4     snale                          running
5  -     snale2                         关闭
```

2、KVM 开关机，重启、强制断电、挂起、恢复、删除及随物理机启动而启动的设置：

2.1、KVM 虚拟机开启（启动）：

```
1 [root@kvm ~ 11:49:26]#virsh start snale2
2 域 snale2 已开始
3 
4 [root@kvm ~ 11:51:31]#virsh list
5  Id    名称                         状态
6 ----------------------------------------------------
7  4     snale                          running
8  5     snale2                         running
```

2.2、重启KVM 虚拟机。要想重启kvm 虚拟机，必须如2.3 ，先在kvm 虚拟机里面安装acpid 服务，并且启动设置为随机启动，否则使用virsh reboot 无效：

```
1 [root@kvm ~ 11:54:01]#virsh reboot snale2
2 域 snale2 正在被重新启动
```

2.3、KVM 虚拟机关机：

```
[root@kvm ~ 11:55:34]#virsh shutdown snale2
域 snale2 被关闭
查看发现还是在运行
[root@kvm ~ 11:56:47]#virsh list
 Id    名称                         状态
----------------------------------------------------
 4     snale                          running
 5     snale2                         running
```

注：KVM 虚拟机默认是无法用virsh shutdown 进行关机的，如果要想使用该命令关机，则必须在kvm 虚拟机上安装acpid acpid-sysvinit 两个包，启动acpid 服务，并且加入随机启动，如下：

```
1 [root@snale ~]# yum install -y acpid acpid-sysvinit
1 [root@snale ~]# service acpid start
2 启动 acpi 守护进程：[确定]
3 [root@snale ~]#  chkconfig --add acpid && chkconfig acpid on
```

将虚拟机重启后，再使用virsh shutdown 即可关机：

```
1 [root@kvm ~ 13:45:11]#virsh shutdown snale2
2 域 snale2 被关闭
3 
4 [root@kvm ~ 13:45:17]#virsh list --all
5  Id    名称                         状态
6 ----------------------------------------------------
7  4     snale                          running
8  -     snale2                         关闭
```

2.4、强制关机（强制断电）：

```
[root@kvm ~ 13:48:07]#virsh list --all
 Id    名称                         状态
----------------------------------------------------
 4     snale                          running
 -     snale2                         关闭

[root@kvm ~ 13:48:16]#virsh destroy snale
域 snale 被删除

[root@kvm ~ 13:48:29]#virsh list --all
 Id    名称                         状态
----------------------------------------------------
 -     snale                          关闭
 -     snale2                         关闭
```

2.5、暂停（挂起）KVM 虚拟机：

```
[root@kvm ~ 13:49:22]#virsh list
 Id    名称                         状态
----------------------------------------------------
 6     snale                          running

[root@kvm ~ 13:49:27]#virsh suspend snale
域 snale 被挂起

[root@kvm ~ 13:50:06]#virsh list
 Id    名称                         状态
----------------------------------------------------
 6     snale                          暂停
```

2.6、恢复被挂起的 KVM 虚拟机：

```
[root@kvm ~ 13:51:05]#virsh resume snale
域 snale 被重新恢复

[root@kvm ~ 13:51:20]#virsh list
 Id    名称                         状态
----------------------------------------------------
 6     snale                          running
```

2.7、删除KVM 虚拟机：

```
[root@kvm ~] virsh undefine snale
```

该方法只删除配置文件，磁盘文件未删除，相当于从虚拟机中移除。

2.8、KVM 设置为随物理机启动而启动（开机启动）：

```
[root@kvm ~ 13:54:26]#virsh autostart snale
域 snale标记为自动开始

 [root@kvm ~ 14:21:25]#virsh autostart --disable snale
 域 snale取消标记为自动开始
```

参考：

https://www.cnblogs.com/chenjiahe/category/845519.html