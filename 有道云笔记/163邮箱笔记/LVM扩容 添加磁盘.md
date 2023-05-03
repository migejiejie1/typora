lvextend 命令用于逻辑卷在线扩容，也就是说lvextend扩容是不需要停机的，应用服务也不需要关闭，其语法格式如下：

```
lvextend  [参数] 	LogicalVolume[Path] [ PhysicalVolumePath... ]
```

常用参数：

```shell
-l +  ：指定逻辑卷的LE个数，如 -l +200
-L + ：表示增加多少空间，如 -L +15G ，单位有bBsSkKmMgGtTpPeE
-l +100%FREE	：表示增加vg的全部可用空间
```

注意：lvextend 扩展后只是扩展了lv的大小，而此时文件系统并未感知到，所有还需要使用``xfs_growfs、resize2fs``等命令来扩展文件系统，``xfs_growf``命令是扩展xfs文件系统，``resize2fs``是扩展ext4文件系统。

```shell
#df -Th 看到的怎么是 /dev/mapper/mysql-lv_data，我们的逻辑卷文件明明是/dev/mysql/lv_data的呀，怎么回事？
#原来这是lvm的mapper机制决定的，当我们lvcreate一个逻辑卷的时候，Linux会新建两个软链接文件，
#如 /dev/VGName/LVName 和 /dev/mapper/VGName-LVName,而这2个文件都是指向/dev/dm-X 块文件的，所有，当我们使用df -h看
#到的 /dev/mapper/mysql-lv_data 其实是和 /dev/mysql/lv_data 一样的，不管我们使用哪个都是可以的。
```

