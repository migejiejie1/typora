```shell
history
相关命令：fc
history 命令可以用来显示曾执行过的命令，也可以根据显示的治疗来重新执行需要的命令

n 显示n个最近的记录
-a 添加记录
-r 读取记录，但不会添加内容记录
-w 覆盖原有的history 文件
-c 清除记录
-d<编号>[n] 删除指定n条记录
-n<文件> 读取指定文件
-r<文件> 读取文件但不记录
-w<文件> 覆盖原有文件 

============================================

history显示历史操作记录，并显示操作时间

1.在用户的目录下的.bash_history文件中
[root@node1 ~]# vi ~/.bash_history

2.直接执行history命令
[root@node1 ~]# history

这两种方式虽然能看到执行的命令，但是不能看出执行的时间，我们进行以下操作，让history能显示执行的时间
编辑/etc/bashrc文件，添加以下四行：
     HISTFILESIZE=2000
     HISTSIZE=2000
     HISTTIMEFORMAT='%F %T '
     export HISTTIMEFORMAT

source /etc/bashrc

注：HISTFILESIZE定义了.bash_history中保存命令的总数，默认是1000，这里改成了2000，HISTSIZE设置了history命令输出最多的记录总数，
HISTTIMEFORMAT定了时间显示格式。


有可能会出现以前的操作记录都会显示更改/etc/bashrc 文件的时间，而不是真正的操作时间，只有更改完/etc/bashrc以后的操作记录会显示正确的时间

分类: linux



```

