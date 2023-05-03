```shell
安装 sysstat

iostat sar来是外部的工具,不是系统默认就安装有的。

可以到http://pagesperso-orange.fr/sebastien.godard/download.html去下载！
 安装
# tar zxvf  xxx.tar.gz

# ./configure

# make

# make install

 

安装首次执行#sar 命令时会提示如下错误。

Cannot open /var/log/sa/sa**: No such file or directory

星号值一般是当天的日期。

这个错误是由于没有创建那个文件，可是使用参数-o 让其生成。

#sar -o 2 3

这样/var/log/sa/目录下就会有文件了。

 

有人说起需要运行sysstat才不会报这个错误，实际上这样做了之后问题依旧。

# chmod 751 /etc/sysconfig/sysstat
# /etc/sysconfig/sysstat
```

