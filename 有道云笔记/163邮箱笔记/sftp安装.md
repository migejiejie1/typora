```shell
在Centos 6.6环境使用系统自带的internal-sftp搭建SFTP服务器。
 
打开命令终端窗口，按以下步骤操作。
 
注：保证系统ssh默认端口为22。
 
1.查看openssh的版本
    ssh -V  
    使用ssh -V 命令来查看openssh的版本，版本必须大于4.8p1，低于的这个版本需要升级。
2.创建sftp组
    groupadd sftp
 
3、创建一个sftp用户，用户名为mysftp，密码为mysftp
    修改用户密码和修改Linux用户密码是一样的。
 
    useradd -g sftp -s /bin/false mysftp    //用户名(自定义:例如巴士在线 bszx)    
    passwd mysftp                           //密码  (bszx)
 
4、sftp组的用户的home目录统一指定到/data/sftp下，按用户名区分，这里先新建一个mysftp目录，
    然后指定mysftp的home为/data/sftp
 
    mkdir -p /data/sftp
    usermod -d /data/sftp mysftp 
 
5、配置sshd_config
    文本编辑器打开 /etc/ssh/sshd_config
    vi /etc/ssh/sshd_config
 
    1)找到如下这行，用#符号注释掉，大致在文件末尾处。
    # Subsystem      sftp    /usr/libexec/openssh/sftp-server 
 
    2)在文件最后面添加如下几行内容，然后保存。
 
        Subsystem       sftp    internal-sftp   
        Match Group sftp   
        ChrootDirectory /data/sftp   
        ForceCommand    internal-sftp   
        AllowTcpForwarding no   
        X11Forwarding no 
 
6、设定Chroot目录权限
 
    chown root:sftp /data/sftp 
    chmod 755 /data/sftp
 
7、建立SFTP用户登入后可写入的目录
    照上面设置后，在重启sshd服务后，用户mysftp已经可以登录。
   但使用chroot指定根目录后，根应该是无法写入的，所以要新建一个目录供mysftp上传文件。这个目录所有者为mysftp，所有组为sftp，所有者有写入权限，而所有组无写入权限。命令如下：
 
    mkdir /data/sftp/upload 
    chown mysftp:sftp /data/sftp/upload
    chmod 755 /data/sftp/upload
   
   
    mkdir /data/sftp/download 
    chown mysftp:sftp /data/sftp/download
    chmod 755 /data/sftp/download
 
8、修改/etc/selinux/config
    文本编辑器打开/etc/selinux/config
 
    vi /etc/selinux/config 
    将文件中的SELINUX=enforcing 修改为 SELINUX=disabled ，然后保存。
    在输入命令:
        setenforce 0 
 
9、重启sshd服务
    输入命令重启服务:
    service sshd restart 
 
10、验证sftp环境
    用mysftp用户名登录，yes确定，回车输入密码。
 
    sftp fjwt@127.0.0.1 
    显示 sftp> 则sftp搭建成功。
   
   
页面系统配置:
        SFTP地址:主机地址
        SFTP用户名:mysftp
        SFTP密码:mysftp
        SFTP路径:/data/sftp
 
 
   
```

