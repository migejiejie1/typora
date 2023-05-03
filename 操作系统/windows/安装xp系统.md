U盘启动盘遇见“inf file txtsetup.sif is corrupt or missing， status 18_”错误

﻿﻿WinXP/Win2003系统ISO镜像文件用UltraISO制作U盘启动盘安装系统时，启动后出现错误提示“inf file txtsetup.sif is corrupt or missing， status 18_”。正确的安装步骤：

--------------------------------------------------------------------------------
1、在PE系统中将WinXP/Win2003系统镜像ISO文件从U盘上复制到本地硬盘，用PE所带的WinRAR程序将该 ISO镜像解压到该分区的根目录下；
2、直接拔出U盘。原因：若不拔出U盘的话，在后续安装复制系统文件时，PE将默认U盘为C盘，这样就会把系统安装文件复制到U盘而不是复制到本地C盘。另因为PE系统实际是运行在内存中，拔出U盘并不会影响PE系统和程序的正常运行；
3、结束上述操作后，进入解压文件目录，双击安装文件便会出现WinXP/Win2003系统安装界面，按照安装界面提示进行即可；
4、安装文件复制完成后，手动重启即可继续安装。

解决方法二：
1、按Del/F2等按键进入BIOS设置；
2、找到SATA Mode，将AHCI改成IDE或Compatible模式，按F10保存。