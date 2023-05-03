```shell
ncdu  计算目录及文件大小的命令
相关命令：du
一个可以替代du命令的工具，ncdu命令是对传统du命令功能上的增强，不需要像du那样输入大量的命令，就可以计算文件及目录大小并可以按照大小或文件名进行排序。它是基于ncurses库开发的，因此还支持很多丰富的交互式命令。


ncdu 1.14 ~ Use the arrow keys to navigate, press ? for help                                                                                                                                                   
--- /root ---------------------------------------------------------
   12.0 KiB [##########]  .bash_history
   12.0 KiB [##########] /.ssh
    8.0 KiB [######    ]  .viminfo
    4.0 KiB [###       ]  anaconda-ks.cfg
    4.0 KiB [###       ]  .bashrc
    4.0 KiB [###       ]  .bash_profile
    4.0 KiB [###       ]  .tcshrc
    4.0 KiB [###       ]  .cshrc
    4.0 KiB [###       ]  .bash_logout
    0.0   B [          ] /.pki                                                                                                                                                                                 


Ncdu还提供了许多操作文件和文件夹的选项－导航，排序甚至删除：
up，k - 用于向上移动光标
down，j - 用于向下移动光标
右键，输入，l> - 打开所选目录
left，<，h - 这将打开父目录
n - 按名称排序（再次按降序排列）
s - 按文件大小排序（再次按降序排列）
d - 删除所选文件或目录
g - 显示百分比和/或图表
t - 排序时在文件之前切换dirs
c - 切换子项目计数的显示
b - 当前目录中的Spawn shell
i - 显示有关所选项目的信息
r - 刷新/重新计算当前目录
q - 退出ncdu
```

