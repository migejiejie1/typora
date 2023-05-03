**Centos7系统根分区空间小, home空间大。怎么删除home分区 增加到分区**

前言
新装centos系统的时候，默认分区（如果你的磁盘比较大），他默认会把 / 根分区分50G，而把其他空间放到/home分区下面，这样的话你的根分区容量就太小了，很容易满，就造成了Centos7系统根分区空间小, /home空间大，那么怎么样把这个/home给他删除扩容到 / 根分区呢？

解决步骤

1. 查看分区

```shell
[root@his ~]# df -Th
文件系统                类型      容量  已用  可用 已用% 挂载点
devtmpfs                devtmpfs  4.2G     0  4.2G    0% /dev
tmpfs                   tmpfs     4.2G     0  4.2G    0% /dev/shm
tmpfs                   tmpfs     4.2G   21M  4.2G    1% /run
tmpfs                   tmpfs     4.2G     0  4.2G    0% /sys/fs/cgroup
/dev/mapper/centos-root xfs        50G   46G  4.2G   92% /
/dev/sda1               xfs      1014M  150M  865M   15% /boot
/dev/mapper/centos-home xfs       745G  2.3G  743G    1% /home
tmpfs                   tmpfs     860M     0  860M    0% /run/user/0
overlay                 overlay    50G   46G  4.2G   92% /var/lib/docker/overlay2/bedd245e5d20bdadbd56af18b9cd0c41e66963ccb0d072f37d1830119e3be190/merged
overlay                 overlay    50G   46G  4.2G   92% /var/lib/docker/overlay2/d28d7d3345cf9cc67bd1da0bd9fc0a9eade720865b8b9f4bd01ba41239ce2d06/merged
overlay                 overlay    50G   46G  4.2G   92% /var/lib/docker/overlay2/fa39b1dd91ee787c522977f6cf9366a836eea7800dbd062f42082d1e2ad126da/merged
```

2. 分区表修改

删掉home分区或者注释掉

```shell
vi /etc/fstab

#
# /etc/fstab
# Created by anaconda on Mon Mar  8 09:39:01 2021
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos-root /                       xfs     defaults        0 0
UUID=9b74bde6-6d0e-4b32-a1af-ac1e4ea927e9 /boot                   xfs     defaults        0 0
#/dev/mapper/centos-home /home                   xfs     defaults        0 0
/dev/mapper/centos-swap swap                    swap    defaults        0 0
```

3. 卸载掉/home分区,然后查看/home分区已经没有了（如果有数据，需要备份，我这里没有数据）

```shell
[root@his ~]# umount /home
[root@his ~]# df -Th
文件系统                类型      容量  已用  可用 已用% 挂载点
devtmpfs                devtmpfs  4.2G     0  4.2G    0% /dev
tmpfs                   tmpfs     4.2G     0  4.2G    0% /dev/shm
tmpfs                   tmpfs     4.2G   21M  4.2G    1% /run
tmpfs                   tmpfs     4.2G     0  4.2G    0% /sys/fs/cgroup
/dev/mapper/centos-root xfs        50G   46G  4.2G   92% /
/dev/sda1               xfs      1014M  150M  865M   15% /boot
tmpfs                   tmpfs     860M     0  860M    0% /run/user/0
overlay                 overlay    50G   46G  4.2G   92% /var/lib/docker/overlay2/bedd245e5d20bdadbd56af18b9cd0c41e66963ccb0d072f37d1830119e3be190/merged
overlay                 overlay    50G   46G  4.2G   92% /var/lib/docker/overlay2/d28d7d3345cf9cc67bd1da0bd9fc0a9eade720865b8b9f4bd01ba41239ce2d06/merged
overlay                 overlay    50G   46G  4.2G   92% /var/lib/docker/overlay2/fa39b1dd91ee787c522977f6cf9366a836eea7800dbd062f42082d1e2ad126da/merged
```

4. 查看有哪些lv逻辑分区

```shell
[root@his ~]# lvscan
  ACTIVE            '/dev/centos/swap' [4.31 GiB] inherit
  ACTIVE            '/dev/centos/home' [<744.68 GiB] inherit
  ACTIVE            '/dev/centos/root' [50.00 GiB] inherit
```

5. 移除/home的lv分区

```shell
[root@his ~]# lvremove /dev/mapper/centos-home
#再看一下
[root@his ~]# lvscan
  ACTIVE            '/dev/centos/swap' [4.31 GiB] inherit
  ACTIVE            '/dev/centos/root' [50.00 GiB] inherit
```

6. 查看一下vg的设置

```shell
[root@his ~]# vgdisplay
  --- Volume group ---
  VG Name               centos
  System ID             
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  5
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               <799.00 GiB
  PE Size               4.00 MiB
  Total PE              204543
  Alloc PE / Size       13904 / 54.31 GiB
  Free  PE / Size       190639 / 744.68 GiB
  VG UUID               poiSdH-FBDX-L8Qb-031s-cHw6-AVy7-qh8HPX
```

可以看到空闲出来的

```shell
  Free  PE / Size       190639 / 744.68 GiB
```

7. 把空闲出来的全部扩展到根路径下

```shell
lvextend -l +100%free /dev/mapper/centos-root
#如果部分用大L，全部用小l
lvextend -L +1T /dev/mapper/centos-root
lvextend -L +200G /dev/mapper/centos-root
```

```shell
[root@his ~]# lvextend -l +100%free /dev/mapper/centos-root
  Size of logical volume centos/root changed from 50.00 GiB (12800 extents) to 794.68 GiB (203439 extents).
  Logical volume centos/root successfully resized.
```

扩容完成之后，他的容量还没有增加，我们需要刷新一下, 执行一个命令

```shell
[root@his ~]# df -Th
文件系统                类型      容量  已用  可用 已用% 挂载点
devtmpfs                devtmpfs  4.2G     0  4.2G    0% /dev
tmpfs                   tmpfs     4.2G     0  4.2G    0% /dev/shm
tmpfs                   tmpfs     4.2G   21M  4.2G    1% /run
tmpfs                   tmpfs     4.2G     0  4.2G    0% /sys/fs/cgroup
/dev/mapper/centos-root xfs        50G   46G  4.2G   92% /
/dev/sda1               xfs      1014M  150M  865M   15% /boot
tmpfs                   tmpfs     860M     0  860M    0% /run/user/0
overlay                 overlay    50G   46G  4.2G   92% /var/lib/docker/overlay2/bedd245e5d20bdadbd56af18b9cd0c41e66963ccb0d072f37d1830119e3be190/merged
overlay                 overlay    50G   46G  4.2G   92% /var/lib/docker/overlay2/d28d7d3345cf9cc67bd1da0bd9fc0a9eade720865b8b9f4bd01ba41239ce2d06/merged
overlay                 overlay    50G   46G  4.2G   92% /var/lib/docker/overlay2/fa39b1dd91ee787c522977f6cf9366a836eea7800dbd062f42082d1e2ad126da/merged
```

8. 刷新,扩大 / 文件系统,时间长短看你的磁盘容量大小

```shell
#如果是centos7是xfs_growfs，如果是centos6是resize2fs
xfs_growfs /dev/mapper/centos-root
```

```shell
[root@his ~]# xfs_growfs /dev/mapper/centos-root
meta-data=/dev/mapper/centos-root isize=512    agcount=4, agsize=3276800 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0 spinodes=0
data     =                       bsize=4096   blocks=13107200, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal               bsize=4096   blocks=6400, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 13107200 to 208321536
```

9. 最后查看

```shell
[root@his ~]# df -Th
文件系统                类型      容量  已用  可用 已用% 挂载点
devtmpfs                devtmpfs  4.2G     0  4.2G    0% /dev
tmpfs                   tmpfs     4.2G     0  4.2G    0% /dev/shm
tmpfs                   tmpfs     4.2G   21M  4.2G    1% /run
tmpfs                   tmpfs     4.2G     0  4.2G    0% /sys/fs/cgroup
/dev/mapper/centos-root xfs       795G   46G  749G    6% /
/dev/sda1               xfs      1014M  150M  865M   15% /boot
tmpfs                   tmpfs     860M     0  860M    0% /run/user/0
overlay                 overlay   795G   46G  749G    6% /var/lib/docker/overlay2/bedd245e5d20bdadbd56af18b9cd0c41e66963ccb0d072f37d1830119e3be190/merged
overlay                 overlay   795G   46G  749G    6% /var/lib/docker/overlay2/d28d7d3345cf9cc67bd1da0bd9fc0a9eade720865b8b9f4bd01ba41239ce2d06/merged
overlay                 overlay   795G   46G  749G    6% /var/lib/docker/overlay2/fa39b1dd91ee787c522977f6cf9366a836eea7800dbd062f42082d1e2ad126da/merged
```

