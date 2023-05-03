```shell
windows 通过命令创建用户并添加到某一个用户组
Intro
最近我们本地增加了一个无界面的 windows 虚拟机，通过 powershell 远程管理，有一些事情就需要通过脚本去处理，记录一下感觉以后可能会用的东西。

通过命令创建用户
net user <userName> <password> /add # net user weihanli Admin1234 /add
添加用户到用户组
net localgroup Administrators #查看 Administrators 用户组信息
net localgroup Administrators username /add # 增加 username 用户到 Administrators 用户组
net localgroup Administrators username /delete # 从 Administrators 用户组删除 username 用户
More
删除用户：


net user username delete # 删除用户名为 username 的用户


```

