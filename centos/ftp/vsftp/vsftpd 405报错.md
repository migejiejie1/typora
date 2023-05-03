```shell
解决vsftpd连接错误425 Security: Bad IP connecting

今天在linux机器上安装了一个vsftpd服务器，结果在连接时出现425 Security: Bad IP connecting错误了，经过一上午的搜索总结出一此问题解决办法。

错误提示

425 Security: Bad IP connecting

主要是需要在/etc/vsftpd/vsftpd.conf文件中添加如下一行：

 代码如下
pasv_promiscuous=YES

service vsftpd restart

pasv_promiscuous选项参数说明：

此选项激活时，将关闭PASV模式的安全检查。该检查确保数据连接和控制连接是来自同一个IP地址。小心打开此选项。此选项唯一合理的用法是存在于由安全隧道方案构成的组织中。默认值为NO。 
合理的用法是：在一些安全隧道配置环境下，或者更好地支持FXP时(才启用它)。


```

