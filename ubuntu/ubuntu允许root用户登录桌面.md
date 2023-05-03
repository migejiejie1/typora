ubuntu 20.04 桌面允许root登录

ubuntu20.04 默认是没有开启root登录的，这在我们桌面操作和配置文件的时候很不方便，于是这里教大家用root直接登录桌面，这样操作系统起来非常方便



一：设置root用户密码

在桌面上右键鼠标选择 Open in Terminal 打开终端模拟器
执行 sudo passwd root
然后输入设置的密码，输入两次，这样就完成了设置root用户密码了

```shell
 sudo passwd root
```



二：修改配置文件

2.1：修改50-ubuntu.conf

执行 sudo vim /usr/share/lightdm/lightdm.conf.d/50-ubuntu.conf 把配置改为如下所示

```shell
sudo vim /usr/share/lightdm/lightdm.conf.d/50-ubuntu.conf 

[Seat:*]
user-session=ubuntu
greeter-show-manual-login=true
all-guest=false #这个可以 不用配置
```

2.1：修改gdm-autologin和gdm-password

执行sudo vim /etc/pam.d/gdm-autologin 注释掉auth required pam_succeed_if.so user != root quiet_success这一行(第三行左右)

```shell
sudo vim /etc/pam.d/gdm-autologin 

#%PAM-1.0
auth requisite pam_nologin.so
#auth required pam_succeed_if.so user != root quiet_success   # 注释掉该行
auth optional pam_gdm.so
auth optional pam_gnome_keyring.so
auth required pam_permit.so
```

执行sudo vim /etc/pam.d/gdm-password注释掉 auth required pam_succeed_if.so user != root quiet_success这一行(第三行左右)

```shell
sudo vim /etc/pam.d/gdm-password

#%PAM-1.0
auth requisite pam_nologin.so
#auth required pam_succeed_if.so user != root quiet_success    # 注释掉该行
@include common-auth
auth optional pam_gnome_keyring.so
@include common-account
```

2.2：修改/root/.profile文件

执行sudo vim /root/.profile修改配置文件如下

```shell
sudo vim /root/.profile

#~/.profile: executed by Bourne-compatible login shells.

if [ “$BASH” ]; then
  if [ -f ~/.bashrc ]; then
    . ~/.bashrc
  fi
fi

tty -s && mesg n || true
mesg n || true
```



三：重启系统使其生效

重启后注销原来用户, 登录选择未列出