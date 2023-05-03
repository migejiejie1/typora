```shell
apt：

命令 作用
apt-cache search package 搜索包
apt-cache show package 获取包的相关信息，如说明、大小、版本等
sudo apt-get install package 安装包
sudo apt-get install package --reinstall 重新安装包
sudo apt-get -f install 强制安装
sudo apt-get remove package 删除包
sudo apt-get remove package - - purge 删除包，包括删除配置文件等
sudo apt-get autoremove 自动删除不需要的包
sudo apt-get update 更新源
sudo apt-get upgrade 更新已安装的包
sudo apt-get dist-upgrade 升级系统
sudo apt-get dselect-upgrade 使用 dselect 升级
apt-cache depends package 了解使用依赖
apt-cache rdepends package 了解某个具体的依赖
sudo apt-get build-dep package 安装相关的编译环境
apt-get source package 下载该包的源代码
sudo apt-get clean && sudo apt-get autoclean 清理下载文件的存档
sudo apt-get check 检查是否有损坏的依赖
 

dpkg：

dpkg -l 查看当前系统中已经安装的软件包的信息

dpkg -L （软件包名称）查看系统中已经安装的软件文件的详细列表
dpkg -s 查看已经安装的指定软件包的详细信息
dpkg -S 查看系统中的某个文件属于那个软件包;
dpkg -i *.deb文件的安装
dpkg -r *.deb文件的卸载;
dpkg -P 彻底的卸载 包括软件的配置文件等等
查看没有安装的deb包命令
dpkg -c 查询deb包文件中所包含的文件 rpm -qlp 
dpkg -I 查询deb包的详细信息
```

