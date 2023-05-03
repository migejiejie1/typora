```shell
weblogic 后台日志报 too many files 解决方案（修改limits.conf文件立即生效)

修改值的时候加上hard值和soft的值
同时需要修改/etc/pam.d/login，添加如下内容：
session    required     pam_limits.so


这个文件中添加这一行表示在登录的时候执行limits操作
ulimit -a：


可以通过ulimit命令设置当前session的limits值：
ulimit -n 10000
对应的参数关系和ulimit -a相关
-a      显示所有限制
-c      core文件大小的上限
-d      进程数据段大小的上限
-f      shell所能创建的文件大小的上限
-m     驻留内存大小的上限
-s      堆栈大小的上限
-t      每秒可占用的CPU时间上限
-p     管道大小
-n     打开文件数的上限
-u     进程数的上限
-v     虚拟内存的上限


######2016年01月05日 补充########
nofile修改立即生效，这个比较有用


修改/proc/sys/fs/file-max的值可以立即更改nofile的值

另外上面提到的修改pam参数，一下几个文件也有相关项目
/etc/pam.d/sshd :
session    required     pam_limits.so
/etc/ssh/sshd_config :
UsePAM yes
/etc/pam.d/login :
session    required     pam_limits.so
/etc/pam.d/system-auth :
session     required      pam_limits.so

(1)/proc/sys/fs/file-max 控制整个系统。
(2)/etc/security/limit.conf控制每个用户, 且受到(1)的限制。
(3)ulimit -n用来控制shell及其子进程, 且受到(2)的限制。

/proc/sys/kernel/threads-max

常用命令： 

查看每个用户最大允许打开文件数量

ulimit -a
其中 open files (-n) 1024 表示每个用户最大允许打开的文件数量是1024


查看当前系统打开的文件数量：
  1. lsof | wc -l  
 2.lsof -p 1234 | wc -l 


设置open files数值方法

ulimit -n 2048   （临时生效，重启后，会丢失）

这样就可以把当前用户的最大允许打开文件数量设置为2048了，但这种设置方法在重启后会还原为默认值。

永久设置方法（或者通过修改/proc/sys/fs/file-max）
 


vim /etc/security/limits.conf
在最后加入   * soft nofile 4096  * hard nofile 4096  

  最前的 * 表示所有用户，可根据需要设置某一用户，例如
     fdipzone soft nofile 8192
     fdipzone hard nofile 8192


详解 ulimit -n 和/proc/sys/fs/file-max区别：
  每个进程中都有一个file descriptor table管理当前进程所访问(open or create)的所有文件，文件描述符关联着open files table中文件的file entry。细节不表，对于open files table能容纳多少file entry。Linux系统配置open files table的文件限制，如果超过配置值，就会拒绝其它文件操作的请求，并抛出Too many open files异常。这种限制有系统级和用户级之分。 

       系统级： 
                系统级设置对所有用户有效。可通过两种方式查看系统最大文件限制 
                1  cat /proc/sys/fs/file-max  
                2  sysctl -a 查看结果中fs.file-max这项的配置数量 
                如果需要增加配置数量就修改/etc/sysctl.conf文件，配置fs.file-max属性，如果属性不存在就添加。 
                配置完成后使用sysctl -p来通知系统启用这项配置 

       用户级： 
                Linux限制每个登录用户的可连接文件数。可通过  ulimit -n来查看当前有效设置。如果想修改这个值就使用 ulimit -n <setting number> 命令。 

              对于文件描述符增加的比例，资料推荐是以2的幂次为参考。如当前文件描述符数量是1024，可增加到2048，如果不够，可设置到4096，依此类推。 

       在出现Too many open files问题后，首先得找出主要原因。最大的可能是打开的文件或是socket没有正常关闭。为了定位问题是否由Java进程引起，通过Java进程号查看当前进程占用文件描述符情况： 
1.lsof -p $java_pid 每个文件描述符的具体属性
2.lsof -p $java_pid | wc -l  当前java进程的file descriptor table中的FD的总量

        分析命令的结果，可判断问题是否由非正常释放资源所引起。 

```

