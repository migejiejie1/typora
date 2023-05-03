```shell
SSH远程登录服务器提示Connection reset by peer怎么解决
1、检查TCP包文件（hosts.allow 和 hosts.deny）

您在用SSH远程登录服务器提示ssh_exchange_identification，可能是因为服务器限制，这时您需检查下/etc/hosts.allow 和 /etc/hosts.deny 配置文件。

默认情况下这两个配置文件会授予所有客户端访问权限，要允许远程访问服务器，您要在/etc/hosts.allow 配置文件中指定其 IP 地址和服务守护进程。例如，要允许 SSH 访问 192.168.2.0 子网中的主机，请添加以下守护程序-客户端对。

sshd: 192.168.2.*

要允许所有单个客户端，请仅指定其 IP 地址。这将只允许客户端使用 SSH 访问服务器并拒绝任何其他客户端访问。

sshd: 192.168.2.5

要允许所有主机 SSH 访问，请添加以下行：

sshd: ALL

如果 /etc/hosts.allow 配置文件没有问题，那么再转到 /etc/hosts.deny 配置文件检查指定的客户端是否被拒绝进入服务器。下图中SSH连接均被拒绝访问。
vim /etc/hosts.deny 
...
sshd: ALL


2、调整SSH配置文件中的连接数限制

出现SSH远程登录服务器失败，也可能是由于SSH连接数被限制了，导致无法连接。通常情况下SSH配置文件中的MaxStartups默认值是10，您可以运行下面命令查看连接数。

$ cat /etc/ssh/sshd_config | grep MaxStartups
#同时允许几个尚未登入的联机画面，所谓联机画面就是在你 ssh 登录的时候，没有输入密码的阶段
#MaxStartups 10:30:100            # start:rate:full

start：表示未完成认证的连接数
rate：当未完成认证的连接数超过 start 时，rate/100 表示新发起的连接有多大的概率被拒绝连接。
full：如果未完成认证的连接数达到 full，则新发起的连接全部拒绝。

如果默认值不能达到要求，您可以将属性设置为更高的值。

$ cat /etc/ssh/sshd_config | grep maxsessions 

maxsessions 1000
同一地址的最大连接数，也就是同一个 IP 地址最大可以保持多少个链接。

如下以上两个参数配置不正确，可能出现如下错误：

ssh_exchange_identification: Connection closed by remote host
```

