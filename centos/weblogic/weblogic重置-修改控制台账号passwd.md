```shell
weblogic10.3.6重置/修改控制台账号密码
weblogic部署服务后由于交接过程中文档不完整导致有一个域的控制台账号密码遗失, 在此整理记录一下重置控制台账号密码的过程：

注：%DOMAIN_HOME%：指WebLogic Server 域(Domain）目录，例如我就是E:\Programs\bea10\user_projects\domains\mobile_domain

一、重置控制台账号密码

1、为了保证操作安全，首先备份%DOMAIN_HOME%/security/DefaultAuthenticatorInit.ldift

2、进入%DOMAIN_HOME%/security目录（windows-shift+右键选择在此处打开命令行窗口，linux-运行客户终端）

　　执行下列命令：java -classpath E:/Programs/bea10/wlserver_10.3/server/lib/weblogic.jar weblogic.security.utils.AdminAccount weblogic weblogic1 .

　　特点注意：最后有个“ .”，一个空格和一个点。其中倒数第二的weblogic代表用户名，最后一个weblogic1代表密码。此命令将生成新文件覆盖%DOMAIN_HOME%/security目录下原来的　DefaultAuthenticatorInit.ldift。

3、进入域的AdminServer目录，如：%DOMAIN_HOME%/servers/AdminServer。将其中的data目录重命名，如：data_old。或者剪切到别的地方。

　　特别注意：删除/移除原data目录的操作是必须的。

4、修改管理服务器的boot.properties文件，路径：%DOMAIN_HOME%/servers/AdminServer/security/boot.properties，修改其中的用户名与密码（用明文，第一次启动服务器时明文将被加密），要与上面命令行中的用户名密码一致（别写反了）。

　　例：修改后：
　　username=weblogic
　　password=weblogic1

5、重新启动服务，就可以使用用户weblogic登录管理控制台了。

 

二、修改控制台帐号的密码

但是有时候我们并不是忘记了密码，而是应管理/安全要求需定期修改控制台密码，相比于正常的修改密码，weblogic算是有些繁琐的，详细方法如下：

1、打开weblogic控制台，安全领域 --> myrealm --> 用户和组，将会看到weblogic用户，可以直接删除，也可以点击用户weblogic进入详情页面，点击口令页面，输入新的口令，保存。

　　如果此时就去重新启动weblogic控制台，是不成功的；

2、需要我们去修改%DOMAIN_HOME%/servers/AdminServer/security/boot.properties文件，将密码修改为在控制台中修改的新密码

　　例：修改后的boot.properties文件：
　　username=weblogic
　　password=weblogic123

注：第一次启动服务器时明文将被加密，不用担心填明文密码会不安全。

3、重新启动服务，就可以使用新密码登录管理控制台了。
```

