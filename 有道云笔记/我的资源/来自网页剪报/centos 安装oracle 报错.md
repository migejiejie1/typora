centos 安装oracle 报Checking swap space  0 MB available, 150 MB required. Fail

```shell
1 系统环境 
centos 6.5
oracle 11g 
内存 16G
硬盘 ssd 250G
2 运行安装命令：
[oracle@localhost database]$ ./runInstaller -silent -responseFile /home/oracle/db_install.rsp
Starting Oracle Universal Installer...
Checking Temp space: must be greater than 120 MB. Actual 150565 MB Passed
Checking swap space: 0 MB available, 150 MB required. Failed <<<<
Some requirement checks failed. You must fulfill these requirements before
continuing with the installation,

Exiting Oracle Universal Installer, log for this session can be found at /tmp/OraInstall2017-06-07_02-08-39PM/installActions2017-06-07_02-08-39PM.log
解决方法：
1、检查 Swap 空间在设置 Swap 文件之前，有必要先检查一下系统里有没有既存的 Swap 文件。运行以下命令：
1 swapon -s
如果返回的信息概要是空的，则表示 Swap 文件不存在。
2、检查文件系统在设置 Swap 文件之前，同样有必要检查一下文件系统，看看是否有足够的硬盘空间来设置 Swap 。运行以下命令：
1df -hal

3、创建并允许 Swap 文件下面使用 dd 命令来创建 Swap 文件。检查返回的信息，还剩余足够的硬盘空间即可。
1ddif=/dev/zero of=/swapfile bs=1024 count=512k
参数解读：if=文件名：输入文件名，缺省为标准输入。即指定源文件。< if=input file >of=文件名：输出文件名，缺省为标准输出。即指定目的文件。< of=output file >bs=bytes：同时设置读入/输出的块大小为bytes个字节count=blocks：仅拷贝blocks个块，块大小等于bs指定的字节数。
4、格式化并激活 Swap 文件上面已经创建好 Swap 文件，还需要格式化后才能使用。运行命令：
mkswap /swapfile

激活 Swap ，运行命令：
swapon /swapfile
以上步骤做完，再次运行命令：
swapon -s
你会发现返回的信息概要：
1Filename                Type        Size    Used    Priority2 /swapfile               file5242840     -1
如果要机器重启的时候自动挂载 Swap ，那么还需要修改 fstab 配置。用 vim 打开 /etc/fstab 文件，在其最后添加如下一行：
1 /swapfile          swap            swap    defaults        00
最后，赋予 Swap 文件适当的权限：
1chown root:root /swapfile 2chmod0600 /swapfile
 参考链接：
https://zhidao.baidu.com/question/1111572259239167739.html
```

