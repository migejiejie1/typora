```shell
timeout 指定运行的命令最大运行时长，时间到则终止该命令。

timeout命令是GNU核心实用程序软件包的一部分，该软件包几乎安装在所有Linux发行版中

如果命令不能在给定的时间内完成，超时工具将强制执行中止命令。下面是语法和示例。
语法timeout DURATION COMMAND [ARG]...

其中Duration是希望命令运行的秒数

s - seconds
m - minutes
h - hours
d - days

常用举例
默认单位是秒

5秒后终止ping操作：
[root@localhost ~]# timeout 5 ping www.baidu.com
 
5分钟之后终止ping操作：
[root@localhost ~]# timeout 5m ping www.baidu.com

1天之后终止ping操作：
[root@localhost ~]# timeout 1d ping www.baidu.com

2.5秒之后终止ping操作：
[root@localhost ~]# timeout 2.5s ping www.baidu.com
 
 
 
发送指定的信号
当达到时间限制时，timeout将SIGTERM信号发送到受管命令。可以使用-s（-signal）选项指定要发送的信号。

5秒钟后timeout 发送SIGKILL信号给ping命令，终止命令：
[root@localhost ~]# sudo timeout -s SIGKILL 5s ping www.baidu.com
 
5秒钟后timeout 发送9号信号（即SIGKILL）给ping命令，终止命令：
[root@localhost ~]# sudo timeout -s 9 5s ping www.baidu.com
 
 
 
想要知道全部可用的信号，请使用 kill -l该命令查看全部的信号。

[root@localhost ~]# kill -l
 1) SIGHUP	 2) SIGINT	 3) SIGQUIT	 4) SIGILL	 5) SIGTRAP
 6) SIGABRT	 7) SIGBUS	 8) SIGFPE	 9) SIGKILL	10) SIGUSR1
11) SIGSEGV	12) SIGUSR2	13) SIGPIPE	14) SIGALRM	15) SIGTERM
16) SIGSTKFLT	17) SIGCHLD	18) SIGCONT	19) SIGSTOP	20) SIGTSTP
21) SIGTTIN	22) SIGTTOU	23) SIGURG	24) SIGXCPU	25) SIGXFSZ
26) SIGVTALRM	27) SIGPROF	28) SIGWINCH	29) SIGIO	30) SIGPWR
31) SIGSYS	34) SIGRTMIN	35) SIGRTMIN+1	36) SIGRTMIN+2	37) SIGRTMIN+3
38) SIGRTMIN+4	39) SIGRTMIN+5	40) SIGRTMIN+6	41) SIGRTMIN+7	42) SIGRTMIN+8
43) SIGRTMIN+9	44) SIGRTMIN+10	45) SIGRTMIN+11	46) SIGRTMIN+12	47) SIGRTMIN+13
48) SIGRTMIN+14	49) SIGRTMIN+15	50) SIGRTMAX-14	51) SIGRTMAX-13	52) SIGRTMAX-12
53) SIGRTMAX-11	54) SIGRTMAX-10	55) SIGRTMAX-9	56) SIGRTMAX-8	57) SIGRTMAX-7
58) SIGRTMAX-6	59) SIGRTMAX-5	60) SIGRTMAX-4	61) SIGRTMAX-3	62) SIGRTMAX-2
63) SIGRTMAX-1	64) SIGRTMAX	
[root@localhost ~]#
 
 

停掉卡住的进程
SIGTERM，当超过时间限制时发送的默认信号可以被某些进程捕获或忽略。在这种情况下，进程在发送终止信号后继续运行。
要确保被执行的的命令终止，请使用-k（--kill after）选项，后面加一个时间。当达到给定的时间限制后会强制结束。

在下面的示例中，timeout命令运行一分钟，如果命令没有结束，将在10秒后终止命令：
[root@localhost ~]# timeout -k 10s 1m sh test.sh

运行在前台
默认情况下，timeout在后台运行托管命令。如果要在前台运行该命令，请使用--foreground选项：
[root@localhost ~]# timeout --foreground 5m ./script.sh
 
```

