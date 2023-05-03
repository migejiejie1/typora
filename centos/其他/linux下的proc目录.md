```shell
Linux下的/proc目录介绍
     proc被称为虚拟文件系统，它是一个控制中心，可以通过更改其中某些文件改变内核运行状态，

它也是内核提空给我们的查询中心，用户可以通过它查看系统硬件及当前运行的进程信息。

Linux中许多工具的数据来源正是proc目录中的内容，比如lsmod的命令是cat /proc/modules的别名。

/proc目录下常用文件介绍：
/proc/loadavg      前三列分别保存最近1分钟，5分钟，及15分钟的平均负载。
/proc/meminfo    当前内存使用信息
/proc/diskstats    磁盘I/O统计信息列表
/proc/net/dev      网络流入流出统计信息
/proc/filesystems  支持的文件系统
/proc/cpuinfo        CPU的详细信息
/proc/cmdline      启动时传递至内核的启动参数，通常由grub进行传递
/proc/mounts     系统当前挂在的文件系统
/proc/uptime    系统运行时间
/poc/version     当前运行的内核版本号等信息


```

