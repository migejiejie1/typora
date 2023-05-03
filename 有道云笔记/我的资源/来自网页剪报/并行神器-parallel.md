```
1、parallel 用法简介
Usage:parallel [options] [command [arguments]] < list_of_argumentsparallel [options] [command [arguments]] (::: arguments|:::: argfile(s))...cat ... | parallel --pipe [options] [command [arguments]]常用选项：::: 后面接参数:::: 后面接文件-j、--jobs   并行任务数-N  每次输入的参数数量--xargs会在一行中输入尽可能多的参数-xapply 从每一个源获取一个参数（或文件一行）--header  把每一行输入中的第一个值做为参数名-m   表示每个job不重复输出“背景”（context）-X   与-m相反，会重复输出“背景文本”-q  保护后面的命令--trim  lr 去除参数两头的空格，只能去除空格，换行符和tab都不能去除--keep-order/-k   强制使输出与参数保持顺序 --keep-order/-k--tmpdir/ --results   都是保存文件，但是后者可以有结构的保存--delay  延迟每个任务启动时间--halt  终止任务--pipe    该参数使得我们可以将输入（stdin）分为多块（block）--block  参数可以指定每块的大小
1.1 输入源
GNU Parallel的输入源支持文件、命令行和标准输入（stdin或pipe）。
# 命令行单输入源[20:41 sxuan@hulab ~]$ parallel echo ::: a b c d e | tee a.txtabcde# stdin（标准输入）作为输入源[20:42 sxuan@hulab ~]$ cat a.txt | parallel echoabcde# GNU Parallel支持通过命令行指定多个输入源，它会生成所有的组合。这在某些需要组合时非常好用[20:45 sxuan@hulab ~]$ parallel echo ::: A B C ::: D E F | tee b.txtA DA EA FB DB EB FC DC EC F# 多个文件作为输入，此时多个文件中的内容也会像上面那样进行组合[20:46 sxuan@hulab ~]$ parallel -a a.txt -a b.txt echo# stdin（标准输入）作为文件源中的一个，使用 -， 输出结果同上[20:52 sxuan@hulab ~]$ cat a.txt |parallel -a - -a b.txt echo# 可以使用::::代替-a，后面可接多个文件名[20:55 sxuan@hulab ~]$ cat a.txt | parallel echo :::: - b.txt# 最后，:::和::::可以同时使用，同样的输出结果也会进行组合[20:55 sxuan@hulab ~]$ parallel echo ::: a b :::: b.txt 
当然，若不想像上面那样进行组合，可使用--xapply参数从每一个源获取一个参数（或文件一行），这个参数有些类似R中的函数，具有广播作用——如果其中一个输入源的长度比较短，它的值会被重复。
[20:57 sxuan@hulab~]$ parallel --xapply echo :::ABC:::DEFADBECF[21:04 sxuan@hulab~]$ parallel --xapply echo :::ABC:::DEFGHIADBECFAGBHCI
1.2 改变参数分隔符
GNU Parallel可以通过--arg-sep和--arg-file-sep指定分隔符替代 ::: 或 ::::，当这两个符号被其它命令占用的时候会特别有用。
[21:18 sxuan@hulab ~]$ parallel -k --arg-sep ,,, echo ,,, a b ,,, c d | tee c.txt a ca db cb d[21:22 sxuan@hulab ~]$ parallel --xapply --arg-file-sep ,,,, echo ,,,, a.txt  b.txt a A Db A Ec A Fd B De B Ea B Fb C Dc C Ed C F
1.3 改变输入分隔符
GNU Parallel默认把一行做为一个参数：使用 \n 做为参数定界符。可以使用 -d 改变：
[21:25 sxuan@hulab~]$ parallel -d b echo :::: a.txt acde
1.4 提前结束和跳过空行
GNU Parallel支持通过-E参数指定一个值做为结束标志：
[21:26 sxuan@hulab~]$ parallel -E stop echo :::AB stop CDAB
GNU Parallel使用 --no-run-if-empty 来跳过空行:
[21:28 sxuan@hulab ~]$ (echo1;echo;echo2)| parallel --no-run-if-emptyecho12
1.5 构建命令行
如果parallel之后没有给定命令，那么这些参数会被当做命令：
[21:29 sxuan@hulab~]$ parallel ::: ls 'echo foo' pwda.txtb.txtc.txtjianchenmypipescriptssnake_testWGS_snakefoo/home/sxuan
此外，命令还可以是一个脚本文件，一个二进制可执行文件或一个bash的函数（须用 export -f 导出函数）
[21:42 sxuan@hulab~]$ echo "echo \$*"> s.sh[21:44 sxuan@hulab~]$ parallel ./s.sh :::"a b c f""1 2 3 4"a b c f1234
1.6 替换字符串
GNU Parallel支持多种替换字符串，默认使用 {}，使用 -I 改变替换字符串符号 {}。其最常见的字符串替换包括以下几种：{.}，去掉扩展名；{/},去掉路径，只保留文件名；{//}，只保留路径；{/.}，同时去掉路径和扩展名；{#}，输出任务编号。同时对于每一个字符串替换都可以自己指定符号：-I对应{}；--extensionreplace替换 {.}；--basenamereplace替换 {/}；--dirnamereplace替换{//}；--basenameextensionreplace替换 {/.}；--seqreplace替换 {#}。
[22:02 sxuan@hulab~]$ parallel echo :::A/B.C; parallel echo {}:::A/B.C; parallel -I,, echo ,,:::A/B.CA/B.CA/B.CA/B.C[22:04 sxuan@hulab~]$ parallel echo {.}:::A/B.C; parallel --extensionreplace ,, echo ,,:::A/B.CA/BA/B[22:08 sxuan@hulab~]$ parallel echo {/}:::A/B.C; parallel --basenamereplace ,, echo ,,:::A/B.CB.CB.C[22:08 sxuan@hulab~]$ parallel echo {//}:::A/B.C; parallel --dirnamereplace ,, echo ,,:::A/B.CAA[22:10 sxuan@hulab~]$ parallel echo {/.}:::A/B.C; parallel --basenameextensionreplace ,, echo ,,:::A/B.CBB[22:13 sxuan@hulab~]$ parallel echo {#} ::: A B C ; parallel --seqreplace ,, echo ,, ::: A B C123123
同时，如果有多个输入源时，可以通过 {编号} 指定某一个输入源的参数：
[22:14 sxuan@hulab~]$ parallel --xapply  echo {1}and{2}:::AB:::CDAandCBandD# 可以使用 / // /. 和 . 改变指定替换字符串[22:14 sxuan@hulab~]$ parallel echo /={1/}//={1//}/.={1/.}.={1.}:::A/B.CD/E.F/=B.C//=A/.=B.=A/B/=E.F//=D/.=E.=D/E# 位置可以是负数，表示倒着数[22:16 sxuan@hulab~]$ parallel echo 1={1}2={2}3={3}-1={-1}-2={-2}-3={-3}:::AB:::CD:::EF1=A2=C3=E-1=E-2=C-3=A1=A2=C3=F-1=F-2=C-3=A1=A2=D3=E-1=E-2=D-3=A1=A2=D3=F-1=F-2=D-3=A1=B2=C3=E-1=E-2=C-3=B1=B2=C3=F-1=F-2=C-3=B1=B2=D3=E-1=E-2=D-3=B1=B2=D3=F-1=F-2=D-3=B
1.7 按列输入和指定参数名
使用 --header 把每一行输入中的第一个值做为参数名。
[22:17 sxuan@hulab~]$ parallel --xapply --header : echo f1={f1} f2={f2}::: f1 AB::: f2 CD| tee d.txtf1=A f2=Cf1=B f2=D
使用 --colsep 把文件中的行切分为列，做为输入参数。
[22:31 sxuan@hulab~]$ perl -e 'printf "f1\tf2\nA\tB\nC\tD\n"'> tsv-file.tsv[22:32 sxuan@hulab~]$ parallel --header :--colsep '\t' echo f1={f1} f2={f2}:::: tsv-file.tsv f1=A f2=Bf1=C f2=D
1.8 多参数
--xargs会在一行中输入尽可能多的参数（与参数字符串长度有关），通过-s可指定一行中参数的上限。
[09:44 sxuan@hulab~]$ perl -e 'for(1..30000){print "$_\n"}'> num30000[09:50 sxuan@hulab~]$ cat num30000 | parallel --xargs echo | wc -l3### 这里官网给出的例子是2行，我的计算机centos7上面的结果是3行，应该跟linux版本有关[09:50 sxuan@hulab~]$ cat num30000 | parallel --xargs -s 10000 echo | wc -l17
为了获得更好的并发性，GNU Parallel会在文件读取结束后再分发参数。
GNU Parallel 在读取完最后一个参数之后，才开始第二个任务，此时会把所有的参数平均分配到4个任务（如果指定了4个任务）。
第一个任务与上面使用 --xargs 的例子一样，但是第二个任务会被平均的分成4个任务，最终一共5个任务。（奇怪的是我的结果与官网教程的结果不一样）
[11:44 sxuan@hulab~]$ cat num30000 | parallel --jobs 4-m echo | wc -l6### 官网教程里这里是5# 10分参数分配到4个任务可以看得更清晰 （这里的结果与官网一致）[11:50 sxuan@hulab~]$ parallel --jobs 4-m echo :::{1..10}12345678910
替换字符串可以是输出字符的一部分，使用-m参数表示每个job不重复输出“背景”（context），-X则与-m相反，会重复输出“背景文本”，具体通过下面几个例子进行理解：
[11:36 sxuan@hulab ~]$ parallel --jobs 4 echo pre-{}-post ::: A B C D E F Gpre-A-postpre-B-postpre-C-postpre-D-postpre-E-postpre-F-postpre-G-post[11:51 sxuan@hulab ~]$ parallel --jobs 4 -m echo pre-{}-post ::: A B C D E F Gpre-A B-postpre-C D-postpre-E F-postpre-G-post[11:57 sxuan@hulab ~]$ parallel --jobs 4 -X echo pre-{}-post ::: A B C D E F Gpre-A-post pre-B-postpre-C-post pre-D-postpre-E-post pre-F-postpre-G-post
使用 -N 限制每行参数的个数，其中-N0表示一次只读取一个参数，且不输入这个参数（作为计数器来使用）。
[12:04 sxuan@hulab~]$ parallel -N4 echo 1={1}2={2}3={3}:::ABCDEFGH1=A2=B3=C1=E2=F3=G[12:05 sxuan@hulab~]$ parallel -N0 echo foo :::123foofoofoo
1.9 引用
如果命令行中包含特殊字符，就需要使用引号保护起来。
perl脚本 'print "@ARGV\n"' 与linux的 echo 的功能一样。
[12:05 sxuan@hulab~]$ perl -e 'print "@ARGV\n"'AA
使用GNU Parallel运行这条命令的时候，perl命令需要用引号包起来，也可以使用-q保护perl命令：
[12:08 sxuan@hulab~]$ parallel perl -e 'print "@ARGV\n"':::This wont work[12:09 sxuan@hulab~]$ parallel -q perl -e 'print "@ARGV\n"':::This worksThisworks[12:10 sxuan@hulab~]$ parallel perl -e \''print "@ARGV\n"'\' :::This works, tooThisworks,too
1.10 去除空格
使用--trim去除参数两头的空格：
[12:10 sxuan@hulab~]$ parallel --trim r echo pre-{}-post :::' A 'pre-A-post[12:12 sxuan@hulab~]$ parallel --trim l echo pre-{}-post :::' A 'pre-A-post[12:12 sxuan@hulab~]$ parallel --trim lr echo pre-{}-post :::' A 'pre-A-post
1.11 控制输出
使用--tag以参数做为输出前缀，使用--tagstring修改输出前缀：
[12:17 sxuan@hulab~]$ parallel --tag echo foo-{}:::ABCA   foo-AB   foo-BC   foo-C[12:19 sxuan@hulab~]$ parallel --tagstring {}-bar echo foo-{}:::ABCA-bar   foo-AB-bar   foo-BC-bar   foo-C
--dryrun作用类似于echo：
[12:19 sxuan@hulab ~]$ parallel --dryrun echo {} ::: A B Cecho Aecho Becho C[12:20 sxuan@hulab ~]$ parallel echo {} ::: A B CABC
--verbose则在运行之前先打印命令：
[12:21 sxuan@hulab ~]$ parallel --verbose echo {} ::: A B Cecho Aecho Becho CABC
一般来说，GNU Parallel 会延迟输出，直到一组命令执行完成。使用--ungroup，可立刻打印输出已完成部分。
[13:45 sxuan@hulab~]$ parallel -j2 'printf "%s-start\n%s" {} {};sleep {};printf "%s\n" -middle;echo {}-end':::4212-start2-middle2-end1-start1-middle1-end4-start4-middle4-end[13:45 sxuan@hulab~]$ parallel -j2 --ungroup 'printf "%s-start\n%s" {} {};sleep {};printf "%s\n" -middle;echo {}-end':::4214-start42-start2-middle2-end1-start1-middle1-end-middle4-end
使用 --ungroup 会很快，但会导致输出错乱，一个任务的行输出可能会被另一个任务的输出截断。像上例所示，第二行输出混合了两个任务： '4-middle' '2-start'。使用 --linebuffer避免这个问题（稍慢一点）：
4-start2-start2-middle2-end1-start1-middle1-end4-middle4-end
强制使输出与参数保持顺序 --keep-order/-k：
[13:53 sxuan@hulab~]$ parallel -j2 -k 'printf "%s-start\n%s" {} {};sleep {};printf "%s\n" -middle;echo {}-end':::4214-start4-middle4-end2-start2-middle2-end1-start1-middle1-end
1.12 将输出保存到文件
GNU Parallel可以把每一个任务的输出保存到文件中，临时文件默认保存在 /tmp 中，可以使用 --tmpdir改变（或者修改 $TMPDIR）：
[13:55 sxuan@hulab~]$ parallel --files :::ABC/tmp/parfmNTJ.par/tmp/parmioFz.par/tmp/pargaTxf.par[13:57 sxuan@hulab~]$ parallel --tmpdir ~--files :::ABC/home/sxuan/parLEXH7.par/home/sxuan/parXsKsR.par/home/sxuan/parZxytI.par[13:58 sxuan@hulab~]$ TMPDIR=~ parallel --files :::ABC/home/sxuan/par2tX6C.par/home/sxuan/parorPJy.par/home/sxuan/pari5TkI.par
输出文件可以有结构的保存 --results，输出文件不仅包含标准输出（stdout）也会包含标准错误输出（stderr）：
[13:59 sxuan@hulab ~]$ parallel --results outdir echo ::: A B CABC[14:00 sxuan@hulab ~]$ tree outdir/outdir/└── 1    ├── A    │   ├── seq    │   ├── stderr    │   └── stdout    ├── B    │   ├── seq    │   ├── stderr    │   └── stdout    └── C        ├── seq        ├── stderr        └── stdout4 directories,9 files
在使用多个变量的时候会显示很有用：
# --header : will take the first value as name and use that in the directory structure.[14:02 sxuan@hulab ~]$ parallel --header :--results outdir echo ::: f1 A B ::: f2 C DA CA DB CB D[14:02 sxuan@hulab ~]$ tree outdir/outdir/└── f1    ├── A    │   └── f2    │       ├── C    │       │   ├── seq    │       │   ├── stderr    │       │   └── stdout    │       └── D    │           ├── seq    │           ├── stderr    │           └── stdout    └── B        └── f2            ├── C            │   ├── seq            │   ├── stderr            │   └── stdout            └── D                ├── seq                ├── stderr                └── stdout9 directories,12 files
1.13 控制执行
使用 --jobs/-j 指定并行任务数。
# 使用64个任务执行128个休眠命令[15:02 sxuan@hulab~]$ time parallel -N0-j64 sleep 1:::{1..128}real    0m2.759suser    0m0.657ssys 0m1.345s# 默认情况下并行任务数与cpu核心数相同, 所以这条命令会比每个cpu两个任务的耗时多一倍[15:03 sxuan@hulab~]$ time parallel -N0 sleep 1:::{1..128}real    0m3.478suser    0m0.656ssys 0m1.344s# 每个cpu两个任务[15:03 sxuan@hulab~]$ time parallel -N0--jobs 200% sleep 1:::{1..128}real    0m2.659suser    0m0.734ssys 0m1.423s# 使用 --jobs 0 表示执行尽可能多的并行任务[15:03 sxuan@hulab~]$ time parallel -N0--jobs 0 sleep 1:::{1..128}real    0m2.135suser    0m0.651ssys 0m1.477s# 除了基于cpu使用率之外，也可以基于cpu数[15:03 sxuan@hulab~]$ time parallel --use-cpus-instead-of-cores -N0 sleep 1:::{1..128}real    1m5.499suser    0m0.950ssys 0m1.897s
1.14 交互
通过使用 --interactive 在一个任务执行之前让用户决定是否执行。
[15:08 sxuan@hulab ~]$ parallel --interactive echo ::: 1 2 3echo 1 ?...yecho 2 ?...yecho 3 ?...y123
1.15 耗时
当job有大量的IO操作时，为避免“惊群效应”，可使用--delay参数指定各个job开始的时间间隔。
[15:16 sxuan@hulab~]$ parallel --delay 2.5 echo Starting{}\;date :::123Starting1TueApr1715:21:41CST2018Starting2TueApr1715:21:44CST2018Starting3TueApr1715:21:46CST2018
若已知任务超过一定时间未反应则为失败则可以通过--timeout指定等待时间避免无谓的等待。GNU parallel能计算所有任务运行时间的中位数，因此可以指定时间为中位数的倍数关系。
[15:35 sxuan@hulab~]$ parallel --timeout 4.1 sleep {}\; echo {}:::246824[15:36 sxuan@hulab~]$ parallel --timeout 200% sleep {}\; echo {}:::2.12.2372.32.12.22.33
1.16 显示任务进度信息
GNU parallel有多种方式可用来动态的显示任务进度信息，如：
parallel --eta sleep :::132213321parallel --progress sleep :::132213321seq 1000| parallel -j10 --bar '(echo -n {};sleep 0.1)'2>>(zenity --progress --auto-kill --auto-close)
使用--joblog参数能够生成各个任务的日志文件：
[15:39 sxuan@hulab ~]$ parallel --joblog /tmp/log exit  ::: 1 2 3 0[15:41 sxuan@hulab ~]$ cat /tmp/log Seq Host    Starttime   JobRuntime  Send    Receive Exitval Signal  Command1   :   1523950890.344       0.018  0   0   1   0   exit 12   :   1523950890.350       0.014  0   0   2   0   exit 23   :   1523950890.357       0.006  0   0   3   0   exit 34   :   1523950890.363       0.006  0   0   0   0   exit 0
通过--resume-failed参数可以重新运行失败的任务; --retry-failed的作用与--resume-failed类似，只是--resume-failed从命令行读取失败任务，而--retry-failed则是从日志文件中读取失败任务：
[15:41 sxuan@hulab ~]$ parallel --resume-failed --joblog /tmp/log exit  ::: 1 2 3 0 0 0[15:48 sxuan@hulab ~]$ cat /tmp/logSeq Host    Starttime   JobRuntime  Send    Receive Exitval Signal  Command1   :   1523950890.344       0.018  0   0   1   0   exit 12   :   1523950890.350       0.014  0   0   2   0   exit 23   :   1523950890.357       0.006  0   0   3   0   exit 34   :   1523950890.363       0.006  0   0   0   0   exit 01   :   1523951289.575       0.029  0   0   1   0   exit 12   :   1523951289.580       0.025  0   0   2   0   exit 23   :   1523951289.585       0.019  0   0   3   0   exit 35   :   1523951289.591       0.013  0   0   0   0   exit 06   :   1523951289.604       0.004  0   0   0   0   exit 0[15:48 sxuan@hulab ~]$ parallel --retry-failed --joblog /tmp/log[15:50 sxuan@hulab ~]$ cat /tmp/logSeq Host    Starttime   JobRuntime  Send    Receive Exitval Signal  Command1   :   1523950890.344       0.018  0   0   1   0   exit 12   :   1523950890.350       0.014  0   0   2   0   exit 23   :   1523950890.357       0.006  0   0   3   0   exit 34   :   1523950890.363       0.006  0   0   0   0   exit 01   :   1523951289.575       0.029  0   0   1   0   exit 12   :   1523951289.580       0.025  0   0   2   0   exit 23   :   1523951289.585       0.019  0   0   3   0   exit 35   :   1523951289.591       0.013  0   0   0   0   exit 06   :   1523951289.604       0.004  0   0   0   0   exit 01   :   1523951445.089       0.013  0   0   1   0   exit 12   :   1523951445.094       0.009  0   0   2   0   exit 23   :   1523951445.102       0.007  0   0   3   0   exit 3
1.17 终止任务
GNU parallel支持在某一情况下（如第一个失败或成功时，或者20%任务失败时）终止任务，终止任务又有两种类型，其一为立即终止（通过--halt now指定），杀死所有正在运行的任务并停止生成新的任务，其二为稍后终止（通过--halt soon指定），停止生成新任务并等待正在运行任务完成。
[15:50 sxuan@hulab ~]$ parallel -j2 --halt soon,fail=1 echo {}\; exit {} ::: 0 0 1 2 3001parallel: This job failed:echo 1; exit 1parallel: Starting no more jobs. Waiting for 1 jobs to finish.2parallel: This job failed:echo 2; exit 2[16:04 sxuan@hulab ~]$ parallel -j2 --halt now,fail=1 echo {}\; exit {} ::: 0 0 1 2 3001parallel: This job failed:echo 1; exit 1[16:05 sxuan@hulab ~]$ parallel -j2 --halt soon,fail=20% echo {}\; exit {} ::: 0 1 2 3 4 5 6 7 8 901parallel: This job failed:echo 1; exit 12parallel: This job failed:echo 2; exit 2parallel: Starting no more jobs. Waiting for 1 jobs to finish.3parallel: This job failed:echo 3; exit 3[16:05 sxuan@hulab ~]$ parallel -j2 --halt now,success=1 echo {}\; exit {} ::: 1 2 3 0 4 5 61230parallel: This job succeeded:echo 0; exit 0
GNU parallel还支持在任务失败后重试运行--retries:
[16:06 sxuan@hulab~]$ parallel -k --retries 3'echo tried {} >>/tmp/runs; echo completed {}; exit {}':::120completed 1completed 2completed 0[16:09 sxuan@hulab~]$ cat /tmp/runs tried 1tried 2tried 0tried 1tried 2tried 1tried 2
关于终止信号的高级用法参考官方入门文档。
1.18 资源限制
GNU parallel能够在开始一个新的任务前检查系统的负载情况防止过载（通过--load可指定负载），同时还能检查系统是否使用了交换空间（swap）（通过--noswap限制使用swap）。
[16:09 sxuan@hulab~]$ parallel --load 100% echo load is less than {} job per cpu :::1load is less than 1 job per cpu[16:19 sxuan@hulab~]$ parallel --noswap echo the system is not swapping ::: nowthe system is not swapping now
同时，对于某些占用内存较多的程序，parallel会检查内存只有内存满足时才启动任务（通过--memfree指定需要内存大小），而且在启动任务后内存不够50%时会杀掉最新开始的任务，直到这个任务完成再重新开始那些杀死的任务。
[16:24 sxuan@hulab~]$ parallel --memfree 1G echo will run if more than 1 GB is::: freewill run if more than 1 GB is free
还可以通过--nice来指定任务的优先级。
[16:27 sxuan@hulab~]$ parallel --nice 17 echo thisis being run with nice -n :::17thisis being run with nice -n 17
1.19 远程操作
可使用-S host来进行远程登陆：
parallel -S username@$SERVER1 echo running on ::: username@$SERVER1
1.20 文件传输
GNU parallel 文件传输使用的是rsync。
echo This is input_file > input_fileparallel -S $SERVER1 --transferfile {} cat ::: input_file
更多远程操作参见入门文档。
1.21 --pipe
--pipe参数使得我们可以将输入（stdin）分为多块（block），然后分配给多个任务多个cpu以达到负载均衡，最后的结果顺序与原始顺序一致。使用--block参数可以指定每块的大小，默认为1M。
[17:15 sxuan@hulab~]$ perl -e 'for(1..1000000){print "$_\n"}'> num1000000[17:16 sxuan@hulab~]$ cat num1000000 | parallel --pipe wc 1656681656681048571149796149796104857214979614979610485721497961497961048572149796149796104857214979614979610485728535285352597465
如果不关心结果顺序，只想要快速的得到结果，可使用--round-robin参数。没有这个参数时每块文件都会启动一个命令，使用这个参数后会将这些文件块分配给job数任务（通过--jobs进行指定）。若想分配更为均匀还可同时指定--block参数。
[17:17 sxuan@hulab~]$ cat num1000000 | parallel --pipe -j4 --round-robin wc 2995922995922097144315464315464209714314979614979610485722351482351481646037[17:23 sxuan@hulab~]$ cat num1000000 | parallel --pipe -j4 --block 2M --round-robin wc 2995932995932097151315465315465209715029959329959320971518534985349597444
```

