**2种Grafana重置admin密码方法**

一、基于命令修改
1）修改密码

```shell
# grafana-cli admin reset-admin-password admin123
```

注意：admin123表示新密码；

2）重启服务

```shell
# systemctl restart grafana-server
```

二、基于修改数据库信息
1）查看Grafana配置文件，确定grafana.db的路径
配置文件路径：/etc/grafana/grafana.ini

```css
[paths]
;data = /var/lib/grafana
 
[database]
# For "sqlite3" only, path relative to data_path setting
;path = grafana.db
```

通过配置文件得知grafana.db的完整路径如下：

```css
/var/lib/grafana/grafana.db
```

或可通过shell的find工具直接全盘查找grafana.db的路径：

```shell
# find / -name "grafana.db"
```

2）使用sqlites修改admin密码
打开数据库

```shell
# sqlite3 /var/lib/grafana/grafana.db
```

修改user表admin用户的password

```shell
#查看数据库中包含的表
.tables
 
#查看user表内容
select * from user;
 
#重置admin用户的密码为默认admin
update user set password = '59acf18b94d7eb0694c61e60ce44c110c7a683ac6a8f09580d626f90f4a242000746579358d77dd9e570e83fa24faa88a8a6', salt = 'F3FAxVm33R' where login = 'admin';
 
#退出sqlite3
.exit
```

好了，可以使用admin、admin登录了。

3）修改指定用户为管理员

```shell
udpate user set is_admin = 1 where login = 'xxxx';
```

修改完成并登录成功后不要忘记把admin用户名密码修改成自己的，不然会被黑的