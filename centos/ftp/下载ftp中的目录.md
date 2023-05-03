**下载ftp中的目录**

  ftp本身不提供目录下载命令，不管get还是mget都只能下载文件，区别在于mget可以下载多个文件，而get只能下载单个文件。

   那么可以选择一个取巧的办法下载ftp服务器中的目录，那就是wget命令

比如ftp服务器(IP:192.16.1.123,端口:21)在test目录下有个download目录，要将其下载下来,假设账户和密码均为ftp。

```shell
$ wget ftp://192.16.1.123:21/test/download --ftp-user=ftp --ftp-password=ftp -r
```

如此即可把整个download目录下载下来。-r用了下载目录



若要将ftp整个下载下来：

```shell
$ wget ftp://192.16.1.123:21/* --ftp-user=ftp --ftp-password=ftp -r
```

*号不能丢，否则只能下载下来一个index索引文件

当然用wget并不能完全解决问题，比如一个Android源码，由于其目录层次太深，太复杂，wget可能会出现下载不完全的情况。



当然向这种下载目录的行为是很傻的，而上传这个目录的人更傻，将目录打个包岂不是利人利己。

下载文件，尤其是zip等二进制文件时，切记将ftp切换到binary模式，否则可能会丢失一些东西。