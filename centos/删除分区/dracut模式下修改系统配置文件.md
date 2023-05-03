**Centos在dracut模式下如何修改系统配置文件**

1、环境介绍
此方法适用于Centos系列的操作系统

2、进入dracut原因
Centos系统进入dracut的原因有很多
比如：
系统重要文件丢失
系统配置文件配置错误等

3、恢复系统文件
3.1、dracut环境
在dracut上下文中是无法看到Centos系统文件的

```shell
dracut#
```

3.2、找到系统盘
Centos默认安装时，系统盘是一个lv设备

```shell
dracut# ls -l /dev/centos/root
```

如果安装时，手动修改了分区，那么可以根据实际情况找到系统设备
比如：

```shell
dracut# ls -l /dev/sda1
```

3.3、挂载系统盘
在dracut上下文中，创建临时目录

```shell
dracut# mkdir tmp1
dracut# mount /dev/centos/root tmp1
```

3.4、修改系统配置文件

```shell
dracut#  cd tmp1
dracut#  ls 
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

此时看到的就是Centos系统上下文了，可以进入/etc目录修改或者恢复配置文件

4、使用此方法解决一个实际问题的示例
Centos进入dracut模式，报 /dev/centos/swap does not exist, 如何恢复