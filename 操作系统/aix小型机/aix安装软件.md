```shell
AIX查看、安装、卸载软件
在AIX下安装软件一般使用installp来进行，但里面有一大堆参数非常繁琐，如果有图形界面就会简单了很多。在图形界面下使用smit installp就会出现图形化界面共选择。有一些选项需要注意。是否仅做预览安装的选项，如果选择了“是”，则仅仅检查安装条件，不进行实际的安装动作。在选项是否接受协议选项中，默认是否，在安装软件需要有协议的时候就不会安装成功。另外还有一个选项是表示在有协议的时候仅做预览，默认是“否”，建议也选择“否”。
例如，
installp -u
installp -a
lslpp -L

smit(ty)进去后可以查看、安装、卸载软件，是图形界面。

软件的组成：
Fileset：一组实现特定功能的文件（Files）的集合。可安装的最小单位。
Package：一组具有相同功能的Filesets的集合。可安装的单个映射（Images）。
LPP：一个包含所有与本LPP有关的Filesets和Packages的完整的软件产品。
Bundle：一组用于特定环境的Filesets和Packages的集合。
Bundle的种类：
App-Dev：应用程序开发所需的程序和工具
Client：在client/server环境下，作为Client运行所需的程序
Graphics-startup：运行X-Windows所需的程序（不如Pers-Prod中的功能全）
Hdwr-Diag：运行硬件测试所需的程序
Pers-Prod：提供完整功能的图形环境所需的程序
Server：在client/server环境下，作为Server运行所需的程序（提供完整的系统管理功能）

软件的三种状态：
Apply：软件处于应用状态，但未被提交（Commit）
Commit：软件已经提交
Reject：软件被从系统中删除

注意：安装软件时，可用如下命令：
# smit easy_install
指定欲安装的Bundles。
一般选择安装App-Dev和Server两个Bundles。

# lslpp [–l | –h] 系统软件列表
-l：列出已安装的软件清单
-h：列出软件安装的历史清单

# lppchk [–c | –v | –l]校验系统软件的正确性和完整性
-c：执行校验和（checksum）及文件大小检查，校验其是否和SVPD（Software Vital Product
Database：软件的重要产品数据库）中的一致
-v：校验系统的三个部分（/、/usr、/usr/share）是否有效，是否有丢失的PTF
-l：校验系统的符号连接（Symbolic Links）是否有改变

# instfix –T [–iv] [–s String | –k Keyword | –f File_Name] [–d Device_Name]安装、查找Fixs
-T：显示完整的Fileset内容清单
-s Sting：显示并查找包含指定的字符串的Fileset
-iv：只显示详细的内容列表，而不进行安装
-k Keyword：安装包含指定关键字（Keyword）或纠错码（Fix）的Fileset
-f File_Name：安装包含多个指定关键字或纠错码的Fileset
-d Device_Name：指定输入设备
如：
显示详细的Fileset内容列表：
# instfix –Tvi
从磁带上安装包含指定关键字的Fileset：
# instfix –k Keyword –d /dev/rmt0.1
从CDROM上查找包含指定字符串的Fileset：
# instfix –s String –d /dev/cd0
AIX下如何卸载已安装的软件?

smitty deinstall
smit desintall

aix中查看软件包(如bos.adt.libm)是否已安装在系统中

#lslpp -l bos.adt.libm

#lslpp -l | grep bos.adt.libm
```

