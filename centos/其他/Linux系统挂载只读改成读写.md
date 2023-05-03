```shell
Linux系统挂载只读改成读写
1、mount命令可用于查看哪个模块输入只读，一般显示为：

[root@localhost ~]# mount
/dev/cciss/c0d0p2 on / type ext3 (rw)
proc on /proc type proc (rw)
sysfs on /sys type sysfs (rw)
devpts on /dev/pts type devpts (rw,gid=5,mode=620)
/dev/cciss/c0d0p7 on /home type ext3 (rw)
/dev/cciss/c0d0p6 on /var type ext3 (rw)
/dev/cciss/c0d0p3 on /usr type ext3 (rw)

2、如果发现有ro，就重新mount，或者umount以后再remount
3、umount /dev/dm-3
　　如果发现有提示“device is busy”，找到是什么进程使得他busy
　　fuser -m /mnt/data 将会显示使用这个模块的pid
　　fuser -mk /mnt/data 将会直接kill那个pid
　　然后重新mount即可。
4、还有一种方法是直接remount，命令为
　　mount -o rw,remount /mnt/data。
　　当系统出现故障进入单用户模式时，通常/分区（根分区）会以只读方式mount，这时候如果只是对其他磁盘执行fsck当然没有问题，但是如果想要修改文件，会发现所有文件都是只读状态，无法修改。好在Linux下的mount命令支持一个remount选项，只需要执行如下命令：

mount / -o rw,remount
　　就可以将根分区重新mount为读写状态，从而可以完成必要的系统配置修改。
```

