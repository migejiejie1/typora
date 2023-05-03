```shell
mkpasswd 的使用

 这个命令是随机生成 密码的一个工具， 如果没有这个命令，请安装相应的包。

  yum -y install expect

 mkpasswd 的使用

  常用的选项， -l 指定 长度
  -d 指定 数字的个数
  -c 指定 小写字符个数 -C 指定大写字符个数
  -s 指定特殊字符个数
  usage: mkpasswd [args] [user]
   where arguments are:
  -l #      (length of password, default = 7)
                     指定密码的长度，默认是7位数
  -d #      (min # of digits, default = 2)
                     指定密码中数字最少位数，默认是2位
  -c #      (min # of lowercase chars, default = 2)
                     指定密码中小写字母最少位数，默认是2位
  -C #      (min # of uppercase chars, default = 2)
                     指定密码中大写字母最少位数，默认是2位
  -s #      (min # of special chars, default = 1)
                     指定密码中特殊字符最少位数，默认是1位
  -v        (verbose, show passwd interaction)
                     这个参数在实验的时候报错，具体不知道。

比如举个例子， 长度 15 位，数字至少 3位， 小写字母至少4 位， 大写字母至少4 位， 特殊字符 至少 2位
 [root@localhost ~]# mkpasswd -l 15 -d 3 -c 4 -C 4 -s 2
```

