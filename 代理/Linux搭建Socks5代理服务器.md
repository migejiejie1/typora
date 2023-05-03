Linux搭建Socks5代理服务器

这种方式要想全局代理就要用类似Proxifier的客户端 ，但是还没找到自动判定只有被墙才用代理的客户端 

Proxifier 不知为啥网页访问Google还是访问不了，但是要它能做游戏代理 ，网页访问还要用类似Proxy SwitchyOmega的插件

所以建议用SS/SSR

安装
1、首先，编译安装SS5需要先安装一些依赖组件

```elm
yum -y install gcc gcc-c++ automake make pam-devel openldap-devel cyrus-sasl-devel openssl-devel
```

2、去官网http://ss5.sourceforge.net/ 下载SS5最新版本的源代码

```elm
wget https://jaist.dl.sourceforge.net/project/ss5/ss5/3.8.9-8/ss5-3.8.9-8.tar.gz
```

3、解压后开始编译安装：

```elm
tar zxvf ./ss5-3.8.9-8.tar.gz
cd ss5-3.8.9
./configure && make && make install
```

4、让SS5随系统一起启动

```elm
chmod +x /etc/init.d/ss5
chkconfig --add ss5
chkconfig --level 2345 ss5 on
```

配置登录方式
修改认证方式  /etc/opt/ss5/ss5.conf

```elm
vi /etc/opt/ss5/ss5.conf
```

删除原来所有配置 添加如下两行

```elm
#        SHost        SPort   Authentication
auth  0.0.0.0/0    -           -

#      Auth     SHost           SPort   DHost           DPort   Fixup   Group   Band    ExpDate
permit - 0.0.0.0/0 - 0.0.0.0/0 - - - - -
```

默认的是：无用户认证。 

如果想要使用用户认证，需要将上面两行修改成下面这样：

```elm
auth 0.0.0.0/0 - u
permit u 0.0.0.0/0 - 0.0.0.0/0 - - - - -
```

添加用户名及密码 

```elm
vi /etc/opt/ss5/ss5.passwd
```

添加用户密码 每行一个用户+密码（之间用空格）

```elm
test1 12345
test2 56789
```

配置端口
修改ss5启动的参数，自定义代理端口 /etc/sysconfig/ss5（如果不设置，默认是1080）  

此文件ss5启动时会主动加载，将

```elm
#SS5_OPTS=" -u root"
```

取消注释，修改成下面这样

```elm
SS5_OPTS=" -u root -b 0.0.0.0:10808"
```

重启ss5

```elm
/etc/rc.d/init.d/ss5 restart
```

也可以用

````elm
service ss5 start
````

启动完成后，可以使用以下命令查看连接情况

```elm
netstat -an | grep 10808
```

查看日志

```elm
more /var/log/ss5/ss5.log
```

关闭ss5

```elm
/etc/rc.d/init.d/ss5 stop
```

也可以用

```elm
service ss5 stop
```



客户端代理软件
Proxifier


如果服务器采用的是windows系统

一种比较常用的搭配是CCProxy（ss5代理服务器）+ Proxifier（客户端）



常用软件代理设置
 一般搭建ss5代理服务器最好使用用户认证的方式（用户名密码），但大多数客户端软件默认都没有此功能(但可以装插件)。
 比如ie浏览器、360安全浏览器、火狐浏览器等。所以如果想要使用这些软件设置sock5代理的话，ss5代理服务器需保持默认的无认证模式。 QQ和遨游浏览器支持用户认证。



火狐(Chrome)

Proxy SwitchyOmega

自动切换规则配置

规则列表网址

```elm
https://raw.githubusercontent.com/int64ago/private-gfwlist/master/gfwlist.txt
```

其它如下图配置

<img src="C:\Users\11650\AppData\Roaming\Typora\typora-user-images\image-20230414151556425.png" alt="image-20230414151556425" style="zoom: 50%;" />