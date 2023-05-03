```
　free 命令相对于top 提供了更简洁的查看系统内存使用情况：
$ free

total            used         free      shared       buffers       cached
Mem:                        255268       238332     16936       0           85540       126384
-/+ buffers/cache:                        26408        228860
Swap:                        265000          0           265000

其中的相关说明：
Mem：表示物理内存统计
-/+ buffers/cached：表示物理内存的缓存统计
Swap：表示硬盘上交换分区的使用情况（这里我们不去关心）
系统的总物理内存：255268Kb（256M），但系统当前真正可用的内存并不是第一行free 标记的 16936Kb，它仅代表未被分配的内存。
我们使用total1、used1、free1、used2、free2 等名称来代表上面统计数据的各值，1、2 分别代表第一行和第二行的数据。
total1：    表示物理内存总量。
used1：     表示总计分配给缓存（包含buffers 与cache ）使用的数量，但其中可能部分缓存并未实际使用。
free1：     未被分配的内存。
shared1：   共享内存，一般系统不会用到，这里也不讨论。
buffers1： 系统分配但未被使用的buffers 数量。
cached1：   系统分配但未被使用的cache 数量。buffer 与cache 的区别见后面。
used2：     实际使用的buffers 与cache 总量，也是实际使用的内存总量。
free2：     未被使用的buffers 与cache 和未被分配的内存之和，这就是系统当前实际可用内存。
　　可以整理出如下等式：
total1 = used1 + free1
total1 = used2 + free2
used1   = buffers1 + cached1 + used2
free2   = buffers1 + cached1 + free1
　　buffer 与cache 的区别
A buffer is something that has yet to be "written" to disk.
A cache is something that has been "read" from the disk and stored for later use.
　　更详细的解释参考：Difference Between Buffer and Cache
对于共享内存（Shared memory），主要用于在UNIX 环境下不同进程之间共享数据，是进程间通信的一种方法，一般的应用程序不会申请使用共享内存，笔者也没有去验证共享内存对上面等式的影响。如果你有兴趣， 请参考：What is Shared Memory?
　　cache 和 buffer的区别：
Cache： 高速缓存，是位于CPU与主内存间的一种容量较小但速度很高的存储器。由于CPU的速度远高于主内存，CPU直接从内存中存取数据要等待一定时间周 期，Cache中保存着CPU刚用过或循环使用的一部分数据，当CPU再次使用该部分数据时可从Cache中直接调用,这样就减少了CPU的等待时间,提 高了系统的效率。Cache又分为一级Cache(L1 Cache)和二级Cache(L2 Cache)，L1 Cache集成在CPU内部，L2 Cache早期一般是焊在主板上,现在也都集成在CPU内部，常见的容量有256KB或512KB L2 Cache。
Buffer：缓冲区，一个用于存储速度不同步的设备或优先级不同的设备之间传输数据的区域。通过缓冲区，可以使进程之间的相互等待变少，从而使从速度慢的设备读入数据时，速度快的设备的操作进程不发生间断。
　　Free中的buffer和cache：（它们都是占用内存）：
buffer: 作为buffer cache的内存，是块设备的读写缓冲区
cache: 作为page cache的内存, 文件系统的cache
　　如果 cache 的值很大，说明cache住的文件数很多。如果频繁访问到的文件都能被cache住，那么磁盘的读IO bi会非常小。
Buffer和Cache的区别
    缓存（cached）是把读取过的数据保存起来，重新读取时若命中（找到需要的数据）就不要去读硬盘了，若没有命中就读硬盘。其中的数据会根据读取频率进行组织，把最频繁读取的内容放在最容易找到的位置，把不再读的内容不断往后排，直至从中删除。
    缓冲（buffers）是根据磁盘的读写设计的，把分散的写操作集中进行，减少磁盘碎片和硬盘的反复寻道，从而提高系统性能。linux有一个守护进程定 期清空缓冲内容（即写如磁盘），也可以通过sync命令手动清空缓冲。举个例子吧：我这里有一个ext2的U盘，我往里面cp一个3M的MP3，但U盘的 灯没有跳动，过了一会儿（或者手动输入sync）U盘的灯就跳动起来了。卸载设备时会清空缓冲，所以有些时候卸载一个设备时要等上几秒钟。
修改/etc/sysctl.conf中的vm.swappiness右边的数字可以在下次开机时调节swap使用策略。该数字范围是0～100，数字越大越倾向于使用swap。默认为60，可以改一下试试。
两者都是RAM中的数据。简单来说，buffer是即将要被写入磁盘的，而cache是被从磁盘中读出来的。
buffer是由各种进程分配的，被用在如输入队列等方面，一个简单的例子如某个进程要求有多个字段读入，在所有字段被读入完整之前，进程把先前读入的字段放在buffer中保存。
cache经常被用在磁盘的I/O请求上，如果有多个进程都要访问某个文件，于是该文件便被做成cache以方便下次被访问，这样可提供系统性能。
Linux的内存管理，实际上跟windows的内存管理有很相像的地方，都是用虚拟内存这个的概念，说到这里不得不骂MS，为什么在很多时候还有很大的物理内存的时候，却还是用到了pagefile. 所以才经常要跟一帮人吵着说Pagefile的大小，以及如何分配这个问题，在Linux大家就不用再吵什么swap大小的问题，我个人认为，swap设个512M已经足够了，如果你问说512M的SWAP不够用怎么办？只能说大哥你还是加内存吧，要不就检查你的应用，是不是真的出现了memory leak.
在Linux下查看内存我们一般用command free
[root@nonamelinux ~]# free
                  total           used       free     shared    buffers     cached
Mem:    386024      3771168908      0           21280     155468
-/+ buffers/cache:     200368    185656
Swap:    393552            0          393552
下面是对这些数值的解释：
第二行(mem)：
total:总计物理内存的大小。
used:已使用多大。
free:可用有多少。
Shared:多个进程共享的内存总额。
Buffers/cached:磁盘缓存的大小。
第三行(-/+ buffers/cached):
used:已使用多大。
free:可用有多少。
第四行就不多解释了。
区别：
第二行(mem)的used/free与第三行(-/+ buffers/cache) used/free的区别。
这两个的区别在于使用的角度来看，第一行是从OS的角度来看，因为对于OS，buffers/cached 都是属于被使用，所以他的可用内存是8908KB,已用内存是377116KB,其中包括，内核（OS）使用+Application(X,oracle,etc)使用的+buffers+cached.
第三行所指的是从应用程序角度来看，对于应用程序来说，buffers/cached 是等于可用的，因为buffer/cached是为了提高文件读取的性能，当应用程序需在用到内存的时候，buffer/cached会很快地被回收。
所以从应用程序的角度来说，可用内存=系统free（ memory+buffers+cached.）
如上例：
185656=8908+21280+155468
接下来解释什么时候内存会被交换，以及按什么方交换。
当可用内存少于额定值的时候，就会开会进行交换.
如何看额定值（RHEL4.0）：
#cat /proc/meminfo
交换将通过三个途径来减少系统中使用的物理页面的个数：　
1.减少缓冲与页面cache的大小，
2.将系统V类型的内存页面交换出去，　
3.换出或者丢弃页面。(Application 占用的内存页，也就是物理内存不足）。
事实上，少量地使用swap是不是影响到系统性能的。
```

