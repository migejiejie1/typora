```shell
使用parted划分GPT分区（fdisk与parted区别）

 parted命令可以划分单个分区大于2T的GPT格式的分区，也可以划分普通的MBR分区，fdisk命令对于大于2T的分区无法划分（大于2.2TB的存储空间用fdisk不支持，需要采用parted来分区），所以用fdisk无法看到parted划分的GPT格式的分区。

 Parted 命令分为两种模式：命令行模式和交互模式。

       1、命令行模式： parted [option] device [command] ,该模式可以直接在命令行下对磁盘进行分区操作，比较适合编程应用。

       2、交互模式：parted [option] device 类似于使用fdisk /dev/xxx

       MBR：MBR分区表(即主引导记录)大家都很熟悉。所支持的最大卷：2T，而且对分区有限制：最多4个主分区或3个主分区加一个扩展分区

       GPT： GPT（即GUID分区表）。是源自EFI标准的一种较新的磁盘分区表结构的标准，是未来磁盘分区的主要形式。与MBR分区方式相比，具有如下优点。突破MBR 4个主分区限制，每个磁盘最多支持128个分区。支持大于2T的分区，最大卷可达18EB。

  parted是一个可以分区并进行分区调整的工具，他可以创建，破坏，移动，复制，调整ext2 linux-swap fat fat32 reiserfs类型的分区，可以创建，调整，移动Macintosh的HFS分区，检测jfs，ntfs，ufs，xfs分区。

    使用方法：parted [options] [device [command [options...]...]]

    options

    -h  显示帮助信息

    -l  显示所有块设备上的分区

    device

    对哪个块设备进行操作，如果没有指定则使用第一个块设备

    command [options...]

    check partition  

    对分区做一个简单的检测

mklabel label-type 

    创建新分区表类型，label-type可以是："bsd", "dvh", "gpt",  "loop","mac", "msdos", "pc98", or "sun" 一般的pc机都是msdos格式，如果分区大于2T则需要选用gpt格式的分区表。


 mkpart part-type [fs-type] start end 

    创建一个part-type类型的分区，part-type可以是："primary", "logical", or "extended" 如果指定fs-type则在创建分区的同时进行格式化。start和end指的是分区的起始位置，单位默认是M。

    eg：mkpart  primary  0  -1   0表示分区的开始  -1表示分区的结尾  意思是划分整个硬盘空间为主分区

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

parted命令常用选项。
当在命令行输入parted后，进入parted命令的交互模式。输入help会显示帮助信息。下面就简单介绍一下常用的功能
1、Check     简单检查文件系统。建议用其他命令检查文件系统，比如fsck
2、Help      显示帮助信息
3、mklabel   创建分区表， 即是使用msdos（MBR）还是使用gpt，或者是其他方式分区表
4、mkfs      创建文件系统。该命令不支持ext3 格式，因此建议不使用，最好是用parted分好区，然后退出parted交互模式，用其他命令进行分区，比如：mkfs.ext3
5、mkpart    创建新分区。
        格式：mkpart PART-TYPE  [FS-TYPE]  START  END
             PART-TYPE 类型主要有primary（主分区）, extended（扩展分区）, logical（逻辑区）. 扩展分区和逻辑分区只对msdos。
             fs-type   文件系统类型，主要有fs32，NTFS，ext2，ext3等
             start end 分区的起始和结束位置。
6、mkpartfs  建立分区及其文件系统。目前还不支持ext3文件系统，因此不建议使用该功能。最后是分好区后，退出parted，然后用其他命令建立文件系统。
7、print    输出分区信息。该功能有3个选项，
       free 显示该盘的所有信息，并显示磁盘剩余空间
     number 显示指定的分区的信息
        all 显示所有磁盘信息
8、resize   调整指定的分区的大小。目前对ext3格式支持不是很好，所以不建议使用该功能。
9、rescue   恢复不小心删除的分区。如果不小心用parted的rm命令删除了一个分区，那么可以通过rescue功能进行恢复。恢复时需要给出分区的起始和结束的位置。然后parted就会在给定的范围内去寻找，并提示恢复分区。
10、rm      删除分区。命令格式 rm  number 。如：rm 3 就是将编号为3的分区删除
11、select  选择设备。当输入parted命令后直接回车进入交互模式是，如果有多块硬盘，需要用select 选择要操作的硬盘。如：select /dev/sdb
12、set     设置标记。更改指定分区编号的标志。标志通常有如下几种：boot  hidden   raid   lvm 等。boot 为引导分区，hidden 为隐藏分区，raid 软raid，lvm 为逻辑分区。如：set 3  boot  on   设置分区号3 为启动分区
```

