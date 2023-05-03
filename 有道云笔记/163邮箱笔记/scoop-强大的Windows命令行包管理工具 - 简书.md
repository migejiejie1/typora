```shell
2020年3月1日调整文章逻辑顺序，增加aria2 16线程下载。
在下载了一系列软件管理专家后，我遇到了scoop这一个神级的软件包管理工具，他会自动设置环境变量，也会管理程序依赖。再添加了仓库之后，基本能满足我的软件安装和管理需求。后期我也会学习一下官方的配置写法，维护一下我需要的一些其他的软件。详情可参见官网，github，github-wiki。
初级玩法：安装scoop用以管理Windows软件
安装前的准备
用户名不含中文字符
Windows 7 SP1+ / Windows Server 2008+
PowerShell 3+
.NET Framework 4.5+
若Powershell或.NET Franmework版本过旧，更新后重启即可。
若不清楚版本号，可Win+R运行powershell，输入以下命令获取版本号。
$PSVersionTable.PSVersion.Major   #查看Powershell版本$PSVersionTable.CLRVersion.Major  #查看.NET Framework版本
安装scoop
Set-ExecutionPolicy RemoteSigned -scope CurrentUseriwr -useb get.scoop.sh | iex
如果下载scoop的过程中断，那么必须先删除(C:\Users\scoop)文件夹，再执行以上命令安装。
下载完成后，输入scoop出现如下帮助即安装成功。
帮助文档
Usage: scoop  []Some useful commands are:alias       Manage scoop aliasesbucket      Manage Scoop bucketscache       Show or clear the download cachecheckup     Check for potential problemscleanup     Cleanup apps by removing old versionsconfig      Get or set configuration valuescreate      Create a custom app manifestdepends     List dependencies for an appexport      Exports (an importable) list of installed appshelp        Show help for a commandhome        Opens the app homepageinfo        Display information about an appinstall     Install appslist        List installed appsprefix      Returns the path to the specified appreset       Reset an app to resolve conflictssearch      Search available appsstatus      Show status and check for new app versionsuninstall   Uninstall an appupdate      Update apps, or Scoop itselfvirustotal  Look for app's hash on virustotal.comwhich       Locate a shim/executable (similar to 'which' on Linux)Type 'scoop help ' to get help for a specific command.
基本操作
查找软件
在安装软件之前，推荐先查询一下。比如我们查询一下git：
scoop search git
在main仓库中找到如下软件：
'main' bucket:    git-annex (7.20190129)    git-crypt (0.6.0-701fb8e)    git-istage (0.2.61)    git-lfs (2.6.1)    git-sizer (1.3.0)    git-town (7.2.0)    git-up (1.6.1)    git-with-openssh (2.20.1.windows.1)    git (2.20.1.windows.1)    git19 (1.9.5-preview20150319)    gitignore (0.2018.08.04)    gitkube (0.3.0)    gitlab-runner (11.7.0)    gitversion (4.0.0)    mingit-busybox (2.20.1.windows.1)    mingit (2.20.1.windows.1)    psgithub (2017.01.22)    psutils (0.2018.08.04)--> includes 'gitignore.ps1'
安装软件
找到git的包名后，我们安装它：
scoop install git
安装成功：
Installing 'git' (2.20.1.windows.1) [64bit]Loading PortableGit-2.20.1-64-bit.7z.exe from cacheChecking hash of PortableGit-2.20.1-64-bit.7z.exe ... ok.Extracting dl.7z ... done.Linking ~\scoop\apps\git\current => ~\scoop\apps\git\2.20.1.windows.1Creating shim for 'git'.Creating shim for 'gitk'.Creating shim for 'git-gui'.Creating shim for 'tig'.Creating shim for 'git-bash'.Creating shortcut for Git Bash (git-bash.exe)Running post-install script...'git' (2.20.1.windows.1) was installed successfully!
安装完成的软件会放在C:\Users\scoop\apps。
利用aria2加速下载
在使用scoop安装aria2后，scoop会自动调用aria2进行多线程下载以加速下载：
scoop install aria2
下载完成后，记得打开16线程（aria2编译版本默认最高线程为16，需要更高的请自行编译）：
scoop config aria2-max-connection-per-server 16scoop config aria2-split16scoop config aria2-min-split-size 1M
卸载软件
scoop uninstall 7zip
更新scoop及软件
scoop update #更新scoopscoop update 7zip #更新7zipscoop * #更新全部
添加仓库
scoop自带的main bucket软件过少，我们需要添加官方维护的extras bucket：
scoop bucket add extras
之后就可以安装我们所需的软件了，附我的安装软件清单：
scoop install calibre gimp inkscape latex zotero
第三方bucket
若在scoop search中找不到需要的软件，可以上github上的第三方bucket查找一下。
比如安装cajviewer，添加bucket：
scoop bucket add scoopbucket https://github.com/yuanying1199/scoopbucket
安装cajviewer：
scoop install scoopbucket/cajviewerlite
```

