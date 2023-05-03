**Centos进入dracut模式，报 /dev/centos/swap does not exist, 如何恢复**

1、问题介绍
本问题是在Centos7.7系统上部署k8s时，关闭了swap，手工删除/dev/centos/swap后出现的，问题本身具有一定的普遍性

2、解决问题
2.1、进入dracut，挂载系统根分区
参考：
https://blog.csdn.net/weixin_43905458/article/details/104059550

2.2、修改/etc/defaut/grub

```shell
dracut# mkdir tmp1
dracut# mount /dev/centos/root tmp1
dracut# vi tmp1/etc/defaut/grub
```

```shell
GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet"
修改为
GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=centos/root rhgb quiet"
```

2.3、挂载boot分区
2.3.1、找到boot分区设备

```shell
dracut# cat tmp1/etc/fstab
#
# /etc/fstab
# Created by anaconda on Thu Jan  2 21:15:59 2020
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos-root /                       xfs     defaults        0 0
UUID=22747de8-b577-4bbb-9b5e-40c1b8c3c504 /boot                   xfs     defaults        0 0
#/dev/mapper/centos-swap swap                    swap    defaults        0 0
```

可以看到boot分区的设备UUID为
22747de8-b577-4bbb-9b5e-40c1b8c3c504

```shell
dracut# blkid
/dev/vda1: UUID="22747de8-b577-4bbb-9b5e-40c1b8c3c504" TYPE="xfs"
/dev/vda2: UUID="T7xTcp-ywWd-ciYc-k9mU-Fd3f-TCfe-wgKQgd" TYPE="LVM2_member"
```

UUID为
22747de8-b577-4bbb-9b5e-40c1b8c3c504对应的设备为/dev/vda1

2.3.2、挂载boot分区设备

```shell
dracut# mkdir tmp2
dracut# mount /dev/vda1 tmp2
```

2.3.2、修改grub.cfg

```shell
dracut# vi /tmp2/grub2/grub.cfg
```

删除下面两处的rd.lvm.lv=centos/[swap](https://so.csdn.net/so/search?q=swap&spm=1001.2101.3001.7020)

```shell
linux16 /vmlinuz-3.10.0-1062.el7.x86_64 root=/dev/mapper/centos-root ro crashkernel=auto rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet LANG=en_US.UTF-8

linux16 /vmlinuz-0-rescue-6f9bcc60986041238dcda79bfef462d5 root=/dev/mapper/centos-root ro crashkernel=auto rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet
```

改为

```shell
linux16 /vmlinuz-3.10.0-1062.el7.x86_64 root=/dev/mapper/centos-root ro crashkernel=auto rd.lvm.lv=centos/root rhgb quiet LANG=en_US.UTF-8

linux16 /vmlinuz-0-rescue-6f9bcc60986041238dcda79bfef462d5 root=/dev/mapper/centos-root ro crashkernel=auto rd.lvm.lv=centos/root rhgb quiet
```

保存退出
重启

```shell
reboot
```

