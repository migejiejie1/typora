CentOS系统启动报错Failed to mount /sysroot解决方法


当服务器未正常关闭，如服务器突然断电等导致服务器关机，再次启动时，服务器无法正常启动，利用提示命令行查看启动日志，发下错误信息

**Failed to mount /sysroot**

 解决办法，在服务器启动命令行输入如下命令即可

**xfs_repair -v -L /dev/dm-0**

注：-L 选项指定强制日志清零，强制xfs_repair将日志归零，即使它包含脏数据（元数据更改）。然后再重启下虚拟机即可


