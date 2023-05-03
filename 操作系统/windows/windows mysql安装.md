```shell
1. zip安装包下载
2.Windows环境配置
	系统变量中添加两个，第一个直接添加Mysql_Home，第二个添加到Path里（注意第二个是/bin文件夹目录）。
3. ini配置文件修改
	刚解压的文件夹中没有选中的两个文件，其中data文件夹在后面初始化时会自动生成，my.ini文件需要自己创建。先创建 .txt文件，添加下面一段进去，再重命名就可以。标明需要改的两个地方要改成自己的路径。
[mysql]
# 设置mysql客户端默认字符集
default-character-set = utf8mb4

[mysqld]
#设置3306端口
port = 3306 
# 设置mysql的安装目录
basedir = C:\Command\mysql_service\mysql8
# 设置mysql数据库的数据的存放目录
datadir = C:\Command\mysql_service\mysql8\data
# 允许最大连接数
max_connections = 20
# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server = utf8mb4
# 创建新表时将使用的默认存储引擎
default-storage-engine = INNODB

4.初始化以及安装
	以管理员身份打开cmd，输入 mysqld --initialize --console 命令进行初始化。
在输出中有一行是：[Note] [MY-010454] [Server] A temporary password is generated for root@localhost: **********
其中root@localhost:后面的“**********”就是初始密码，初次登陆时要用到。
接下来 mysqld --install 命令行安装。遇到问题可以看第6步是否有解决办法。
5.启动和修改密码
安装完成后，输入 net start mysql 命令启动mysql。
然后 mysql -u root -p 登录， 密码是第4步中的初始密码，
如果想改密码，可以用 ALTER USER USER() IDENTIFIED BY ‘NewPassword’;，命令最后的分号要加，NewPassword是要改的新密码。
退出mysql： quit；
关闭服务： net stop mysql
6.遇到的问题
1）由于找不到VCRUNTIME140_1.dll，无法继续执行代码。重新安装程序可能会解决此问题
解决方法
2）执行 mysql --install 时出现：Install/Remove of the Service Denied
权限问题，要用管理员身份运行cmd（命令提示符）
3）执行 mysql --install 时出现：The service already exists
是因为之前已经安装过mysql并且没有删除干净。清理之前的安装痕迹并不仅仅是删除 /data 或其他文件夹就能解决的问题。以管理员身份运行cmd，
输入 sc query mysql 查看名为mysql的服务：
发现mysql服务已经存在，则先删除服务： sc delete mysql，之后再安装。
4）在 mysql -u root -p 之后出现：Access denied for user ‘root’@‘localhost’ (using password: YES)
出现这个应该是输入的初始密码错误，也就是第4步的密码输入的时候出现了问题。
我当时输入密码时显示出来的都是****** 字符，也不知道哪里输错，后来百度看到有些文章说在my.ini文件尾加上skip-grant-tables，这样的话不用输入密码就直接可以登录进去， 可我试过之后又出现了新的问题：Can’t connect to MySQL server on ‘localhost’ (10061) 。此时如果能在 /data 文件夹里的 电脑名.err 文件里找到 A temporary password is generated for root@localhost:**********，那么 ******代表的就是原始密码。我当时 .err 文件里找不到这句话，所以删除重装了。。。。。
```

