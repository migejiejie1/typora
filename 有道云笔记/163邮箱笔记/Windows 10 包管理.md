```shell
很长时间没摸windows了， 发现自己居然out了，windows也有类似mac的brew包管理器，
Scoop
scoop环境：
用户名不含中文字符
Windows 7 SP1+ / Windows Server 2008+
PowerShell 5+
. NET Framework 4.5+
$PSVersionTable.PSVersion.Major   #查看Powershell版本$PSVersionTable.CLRVersion.Major  #查看.NET Framework版本
安装 scoop
# 开启脚本权限Set-ExecutionPolicy RemoteSigned -scope CurrentUser# 安装iwr -useb get.scoop.sh | iex#orInvoke-Expression (New-Object System.Net.WebClient).DownloadString('https://get.scoop.sh')
你可能需要VPN (fan qiang)如果下载scoop的过程中断，那么必须先删除(C:\Users\scoop)文件夹，再执行以上命令安装。
帮助文档
Usage: scoop  []Someuseful commands are:alias管理 scoop 别名bucket管理 Scoop bucketscache显示/清理下载缓存checkup检查可能存在的问题cleanup移除旧版本清理应用config获取或设置配置值create创建一个自定义的app manifestdepends列出一个app的依赖关系export导出（可导入的）已安装应用程序列表help显示一个命令的帮助home打开一个app 的主页info显示一个app的相关信息install安装 appslist列出已安装的 appsprefix返回指定应用程序的路径reset重置应用程序以解决冲突search搜索可用应用status显示状态并检查新的应用程序版本uninstall卸载 appupdate更新 apps 和更新 Scoopvirustotal在virustotal.com上查找应用程序的哈希which找到一个shim/可执行文件（类似于Linux上的which）
输入 'scoop help' 显示特定的命令
基本操作
使用scoop安装 git
# 查找scoopsearch git# 安装scoopinstall git#卸载scoopuninstall git# 更新scoopupdate git
添加仓库
scoop自带的main bucket软件过少，我们需要添加官方维护的extras bucket：
scoop bucket add extras
第三方 bucket
若在scoop search中找不到需要的软件，可以上github上的第三方bucket查找一下。 比如安装cajviewer，添加bucket：
# 添加仓库scoop bucket add scoopbucket https://github.com/yuanying1199/scoopbucket# 安装scoop install scoopbucket/cajviewerlite
下载加速
方便是最大的优点，缺点就是太尼玛慢了， 装个mysql，中间上了个大号，抽了 3 根烟，还没装完，最后快到 80%的时候，安装失败。泪奔了...
#安装aria2，scoop install aria2#打开16线程，aria2编译版本默认最高线程为16，需要更高的请自行编译scoop config aria2-max-connection-per-server16scoop config aria2-split16scoop config aria2-min-split-size1M
使用aria2下载 速度飞起，赞一个。
以上 scoop 应该就差不多了，够用了。
Chocolatey
Chocolatey是一款专为 Windows 系统开发的、基于 NuGet 的包管理器工具，类似于Node.js的npm，MacOS的brew，Ubuntu的apt-get，它简称为choco。Chocolatey的设计目标是成为一个去中心化的框架，便于开发者按需快速安装应用程序和工具。
Chocolatey 的官网：https://chocolatey.org/
所需环境
Windows 7+ / Windows Server 2003+
PowerShell v2+
. NET Framework 4+
Chocolatey 安装
请使用管理员身份打开控制台。
使用 cmd
@powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
使用 PowerShell
Set-ExecutionPolicy Bypass -Scope Process -Forceiex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))
相关命令
* -? 帮助文档* list - 列出本地或者远程包* find - 查找本地或者远程包* search - 搜索本地或者远程包* info - 查看包信息* install - 安装包* pin - 使一个包不被升级。* outdated - 检索已经过时的包* upgrade - 升级包* uninstall - 卸载* pack - 打包编译nupkg包* push - 发布一个已经编译的 nupkg包* new - 从模板新建一个包* sources - 查看和配置默认源（源的别名）* source - 查看和配置默认源* config - 产看并配置配置文件设置* feature - 查看和配置choco功能* features - 查看和配置choco功能 (源的别名)* setapikey - 检索、保存或删除特定源的apikey（apikey的别名）* apikey - 检索、保存或删除特定源的apikey* unpackself - have chocolatey set itself up* version - [已经弃用]* update - [不推荐]备用升级命令
常用的一些命令
# 列出本地已安装的包choco list --local-only#列出Windows系统已安装的软件choco list -li  #or  choco list -lai#升级所有已安装的包choco upgrade all -y#安装Ruby Gem -choco install compass -source ruby#安装Python Egg -choco install sphynx -source python#安装IIS服务器特性 -choco install IIS -source windowsfeatures#安装Webpi特性 -choco install IIS7.5Express -source webpi
Cygwin
Cygwin是一个在windows平台上运行的类 UNIX 模拟环境
安装
右键点这里下载，下载完成之后，安装：
网络连接方式：Direct Connection
User URL:http://mirrors.163.com/cygwin/或者http://mirrors.neusoft.edu.cn/cygwin/=>Add
初始安装包
在Select Packages的时候，一次搜索并选择最高版本，tcl,expect,rsync，lynx... (... . 假如你需要的话... )。
然后执行 ：
#安装 apt-cyglynx-source rawgit.com/transcode-open/apt-cyg/master/apt-cyg > apt-cyg# 把apt-cyg 链接到bininstallapt-cyg /bin
以上是懒癌方式安装apt-cyg, 当然也可以手动安装 ：
$ git clone https://github.com/transcode-open/apt-cyg.git$cd apt-cyg$ install apt-cyg /bin
这样一来就可以使用apt-cyg管理 包了
# 使用 apt-cyg 安装 `nano` 包apt-cyg install nano
* install     安装包* remove      删除包* update      更新包* download    下载包，但是不会安装和更新* show        显示指定包的信息* depends     为包生成依赖关系树。* rdepends    生成依赖于命名包的包树。* list        列出指定的包* listall     列出所有包含包名的包* category    显示 category 列表的包* listfiles   列出给定包拥有的所有文件。可以指定多个包* search      搜索拥有指定文件的下载包* searchall   搜索所有拥有指定文件的下载包
结语
我推荐使用scoop, 自行感受吧。
```

