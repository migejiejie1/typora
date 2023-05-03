```shell
dstat
相关命令：iostat,vmstat
dstat -- 多功能系统资源监控工具。

用法：dstat [-afv] [options..] [delay [count]]

常用参数：

　　-c, --cpu              显示CPU情况

　　-C 0,3,total           统计指定CPU或汇总信息

　　-d, --disk             显示磁盘情况

　　-D total,hda           统计指定磁盘或汇总信息

　　-g, --page             enable page stats

　　-i, --int              显示中断统计

　　-I 5,eth2              统计系统负载情况，包括1分钟、5分钟、15分钟平均值

　　-l, --load             enable load stats

　　-m, --mem              显示内存情况

　　-n, --net              显示网络情况

　　-N eth1,total          可以指定网络接口

　　-p, --proc             统计进程信息，包括runnable、uninterruptible、new

　　-r, --io 		     统计I/O请求，包括读写请求

　　-s, --swap             显示swap情况

　　-S swap1,total         可以指定多个swap

　　-t, --time             显示统计时时间，对分析历史数据非常有用

　　-y, --sys              统计系统信息，包括中断、上下文切换

　　--ipc                  报告IPC消息队列和信号量的使用情况

　　--lock                 统计lock信息

　　--raw                  统计raw信息

　　--tcp                  统计tcp信息

　　--udp                  统计udp信息

　　--unix                 统计unix信息

　　-M stat1,stat2         统计external信息
         --mods stat1,stat2

　　-a, --all              使用-cdngy 缺省的就是这样显示

　　-f, --full             使用 -C, -D, -I, -N and -S 显示

　　-v, --vmstat           使用-pmgdsc -D 显示

　　--integer              show integer values

　　--nocolor              不使用颜色功能

　　--noheaders            只显示一次表头以后就不显示了,使用重定向写入文件时很有用

　　--noupdate             disable intermediate updates

　　--output file          写入到CVS文件中

-----------------------------------------------------------------------
[root@manage ~]# dstat -icdmnspty --udp --tcp 
----interrupts--- ----total-cpu-usage---- -dsk/total- ------memory-usage----- -net/total- ----swap--- ---procs--- ----system---- ---system-- --udp-- ----tcp-sockets----
  33    35    36 |usr sys idl wai hiq siq| read  writ| used  buff  cach  free| recv  send| used  free|run blk new|     time     | int   csw |lis act|lis act syn tim clo
   0     1     0 |  0   0 100   0   0   0| 676B   13k|1181M  289M 7775M 6640M|   0     0 |   0  1000M|  0   0 1.4|07-07 18:03:45| 298   499 |  5   0|  6   7   0   1   2
   1     1     0 |  0   0 100   0   0   0|   0     0 |1181M  289M 7775M 6639M| 102B  160B|   0  1000M|  0   0 1.0|07-07 18:03:46| 315   540 |  5   0|  6   7   0   1   2
   0     1     0 |  0   0 100   0   0   0|   0    20k|1181M  289M 7775M 6639M|  56B 1802B|   0  1000M|  0   0 1.0|07-07 18:03:47| 299   525 |  5   0|  6   7   0   1   2

```

