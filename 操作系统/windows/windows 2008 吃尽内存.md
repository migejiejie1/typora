```shell

windows2008吃尽内存的解决办法
最近才用上windows2008，之前一直用的是windows2003，发现系统运行一段时间后，内存吃紧，赶紧打开资源查看器，发现当前运行的程序占有内存都很小，后经查资料，原来是被windows2008的文件缓存吃尽了，这是windows2008的新机制，因此解决思路也就是限制文件缓存的大小，步骤如下：

1 、下载DynCache，微软官网地址；

2、解压文件，将\DynCache\Retail\AMD64\DynCache copy 到 system32下；
3、 创建服务；
sc create DynCache binpath= %SystemRoot%\System32\DynCache.exe start= auto type= own DisplayName= "Dynamic Cache Service"
4、双击执行DynCache.reg；
5、打开注册表Regedit,路径:HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\DynCache\Parameters，设置MaxSystemCacheMBytes的值为600（单位MB），注意是十进制；OK
5、启动服务DynCache；
6、重启计算机；

------------如需卸载----------------
sc stop DynCache
sc delete DynCache


Microsoft Windows Dynamic Cache Service  / 动态缓存服务
```

