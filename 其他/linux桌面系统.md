```shell
linux桌面系统：

1、介绍：

   CentOS作为服务器的操作系统是很常见的，但是因为需要稳定而没有很时髦的更新，所以很少做为桌面环境。在服务器上通常

不需要安装桌面环境，最小化地安装 CentOS（也就是 minimal CentOS） 就可以了。不过在最小化安装的 CentOS 中通过YUM

来安装桌面环境也是非常方便的。

   CentOS6主要的桌面环境：Desktop、Desktop Platform、KDE Desktop、X Window System；

   CentOS7主要的桌面环境：KDE Plasma Workspaces、Gnome Desktop；

   #kde桌面环境比gnome桌面环境要美观；

2、安装图形桌面环境yum命令：

（1）Centos7：

1）安装KDE桌面环境：

   yum groupinstall "KDE Plasma Workspaces"

   #注意：因为KDE Plasma Workspaces软件包名称中间包含有空格，需要用引号引起来才行；

2）安装GNOME环境；

   yum groupinstall "GNOME Desktop"

（2）CentOS6：

1）安装KDE桌面环境：

   yum groupinstall "X Window System" "KDE Desktop" Desktop

2）安装Gnome桌面环境：

   yum groupinstall "X Window System" "Desktop Platform" Desktop

3、安装桌面环境所需的字体及管理工具：

   yum groupinstall -y "Fonts"

   yum install -y dejavu-lgc-sans-fonts

   yum -y groupinstall "Graphical Administration Tools"

   yum -y groupinstall "Internet Browser"

   yum -y groupinstall "General Purpose Desktop"

   yum -y groupinstall "Office Suite and Productivity"

   yum -y groupinstall "Graphics Creation Tools"




```

