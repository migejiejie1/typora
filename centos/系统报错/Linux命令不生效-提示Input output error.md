# Linux命令不生效—提示:Input/output error



需要将一台Linux服务器重启，发现命令不生效。

1.首先确认使用账户为root权限。

成功登录后在输入命令前的标识：**$表示普通用户，#表示超级用户（root）。**



2.使用root用户执行reboot、shutdown一系列重启、关机命令不生效。提示：**Input/output error**

```bash
bash: /sbin/reboot: Input/output error
```

我碰到的是好多命令都无法正常使用，ls、fdisk等命令，也提示Input/output error。

这可能为硬盘坏道、锁死问题，导致shell命令不生效。



**3.解决方法：内核直接重启，不需要读取(已经锁死或坏掉的)硬盘**

**执行命令：**

```powershell
echo 1 > /proc/sys/kernel/sysrq
```

"magic SysRq key"提供了一个通过/proc 来直接给内核发送命令的方法。要启用该特性，只需在内核编译的时候启用"CONFIG_MAGIC_SYSRQ"这个选项，而一般发行版的标准内核都已经启用了。执行第一步该命令激活这个选项。

**接着执行命令：**

```powershell
echo b > /proc/sysrq-trigger
```

执行完后设备将重启。