# [linux强制用户下线](https://www.cnblogs.com/yangxia-test/p/4371066.html)

Linux系统为多用户多任务系统，因此允许多个用户登录到系统，有时候，我们需要强制某些用户下线.

 

**前提**：必须是root权限
**操作**：
（1）使用who查看目前有哪些用户登录了服务器，见下图

```
[root@vm18 ~]# who
root     pts/0        2015-03-27 10:23 (192.168.9.188)
```

从上文可以看出用户root使用ip地址为192.168.9.188登录到linux系统上

 

（2）看看root都在什么时间登录过系统 

```
[root@vm18 ~]# last root
root     pts/0        192.168.9.188    Fri Mar 27 10:23   still logged in    
```

 

（3）使用pkill -kill -t pts/0命令踢出第一个用户，即强制下线。

```
[root@vm18 ~]# pkill -kill -t pts/0
```

命令解释：pts/1 对应的是该用户的TTY。