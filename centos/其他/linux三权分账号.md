**linux三权分账号**

Computer Administrator Supervisor 计算机管理员主管 / compadminsupv 管理员
Computer Auditing Supervisor 计算机审计主管 / compauditsupv 审计员
Computer Operations Supervisor 计算机操作主管 / compopssupv 操作员

创建用户:

```shell
useradd -u 5001 -s /bin/bash compadminsupv
useradd -u 5002 -s /bin/bash compauditsupv
useradd -u 5003 -s /bin/bash compopssupv
```

管理员: 拥有所有操作权限
	给 compadminsupv 用户授权，修改/etc/sudoers文件，添加如下内容:

```shell
vim /etc/sudoers
compadminsupv    ALL=(ALL)       ALL  #非免密使用
或compadminsupv	 ALL=(ALL)       NOPASSWD: ALL  #免密使用
```

审计员: 对各类日志及文件只具有查看权限
	审计账号只用于审计功能，其权限可在普通账号基础上进行修改

1. 修改审计账号权限使其只具有查看功能

```shell
setfacl -m u:compauditsupv:rx /*   #对 / 目录设置权限控制
或setfacl -m u:compauditsupv:rx /var/log/*  #对 /var/log/ 目录设置权限控制
或setfacl -R -m u:compauditsupv:rx /var/log/* #对 /var/log/ 目录及下级目录设置权限控制

setfacl -b /var/log/  #删除所有扩展ACL表项
```

2. 给 compauditsupv 用户授权，修改/etc/sudoers文件，在文件最下边添加如下内容  **不需要配置**

```shell
vim /etc/sudoers
compauditsupv ALL = (root) /usr/bin/cat , /usr/bin/less , /usr/bin/more , /usr/bin/tail , /usr/bin/head  #非免密使用
或compauditsupv ALL = (root) NOPASSWD: /usr/bin/cat , /usr/bin/less , /usr/bin/more , /usr/bin/tail , /usr/bin/head   #免密使用
```

操作员: 具有基本的操作权限
	普通账号权限具体需要根据实际需求设置，一般已有的权限可以满足需求（通过sudo授权控制权限）

配置密钥登录

```shell
if [ ! -d /home/compadminsupv/.ssh/ ]; then mkdir -p -m 700 /home/compadminsupv/.ssh/; fi
if [ ! -f /home/compadminsupv/.ssh/authorized_keys ]; then touch /home/compadminsupv/.ssh/authorized_keys; chmod 600 /home/compadminsupv/.ssh/authorized_keys; fi
sed -i '/4096-051821/d' /home/compadminsupv/.ssh/authorized_keys
echo 'ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAgEA8H03C9QmvjArm2I7z+LcfQWlcmLDJcKxCFSEHVq4sw8G2WekyH0uRnJ5keelwmr2r8XqM2BOE8cGNkuECjaQEnKXSejixFzNQHF7arFG9wj3H5LE+yw95eP2o+x6z/G/5PSKVv6cz8KvHE0Z/WJFaNvBKAKYmLqa2DiR2o9rwUJ37lEH8AKVEtY+PwJxTC0YSKVI7AINOX7YweFDkQnivI0GsBcvzUboToT2EIL+4JMMfkDNDyPbTRudFEkPL27tGLI/DjT6nBHSRL6HosD9hrci6c0Jcju5WZbs1zgYEm5kHL6f++IIphaPQsQ68zys2Mn66YVKzIsxWzn5I/5/LWVo4uWJ6Bb9jAmkJs2V3JzfyOXqeUqED9a0x4eD2B/TF5iz/CsmdP1+YULc2PdQ1WRZBUKkh+D8mzsiCQvXjBkIjNT8zvm7VtstAdovz08x25xvvfTy4nRLjeZMGCPRpp8qfY7DLu6ia82GVLvuqfilkM2mNWfCMivPDs4oKtZAZGREbFpIUPYiOUbrz3TrOEntG37sI3YKh7mJDOUuO1aTHwTbMGYJtaABBftez0Jm8woE5kGmXp9UNSIbVp43pKJN4AUqHcDMaZdvAQs+Eju/bCBufPiUOkFBeiKvT4gnF8EV7sj7mvnqK1nAcc9HwTUw3xYP+ZIifRlW6jT70ck= rsa 4096-051821' >> /home/compadminsupv/.ssh/authorized_keys
chown -R compadminsupv:compadminsupv /home/compadminsupv/.ssh/
```