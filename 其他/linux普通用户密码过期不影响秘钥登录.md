```shell
linux普通用户密码过期不影响秘钥登录及设置sftp不可登录

一、需求
今天听朋友说了一个需求，大意是在使用sftp传文件，很多个账户同时使用，因为安全需求密码必须90天更换，原本是用秘钥登录可以绕过更换密码的要求，但是客户却要求只能使用密码登录，这样如果90天更换就属实让人头疼，所以就想同时提供密码和秘钥，如果密码90天过期，让客户通过秘钥登录自己修改密码，这样可以节省人力。但是在使用的时候却发现密码一旦过期，秘钥一样会提示修改密码。后来经过查资料发现pam在发现密码过期之后不管是密码登录还是秘钥登录都会给sshd发送一个密码过期的警告，而当sshd收到这个过期的消息后就会中断登录，导致秘钥也无法登陆。

二、查询到的资料描述
The order of operations that causes the expired password prompt is as follows:

SSH runs the PAM  stage, which verifies that the account exists and is valid. The  stage notices that the password has expired, and lets SSH know.accountaccount
SSH performs key-based authentication. It doesn’t need PAM for this, so it doesn’t run the  stage. It then sets up the SSH login session and runs the PAM  stage.authsession
Next, SSH remembers that PAM told it the password had expired, prints a warning message, and asks PAM to have the user change the password. SSH then disconnects.
All of this is SSH’s doing, and I don’t see any SSH options to configure this behavior. So unless you want to build a custom version of SSH and/or PAM, the only option I see is to prevent PAM from reporting the expired password to SSH. If you do this, it will disable expired password checks over SSH entirely, even if the user is logging in over SSH with a password. Other (non-SSH) methods of login will still check password expiration.

Your current  file has a  entry. I presume there’s a  file which contains a reference to . This is the line that checks for an expired password.pam.d/sshdaccount include common-accountcommon-accountpam_unix.so

You probably don’t want to touch the  file itself, since it’s used for other login methods. Instead, you want to remove the  from your  file. If there are other functions in  besides , you probably want to put them directly into .common-accountincludepam.d/sshdcommon-accountpam_unix.sopam.d/sshd

Finally, remember that this is a modification to the security of your system and you shouldn’t just blindly trust me to give you good advice. Read up on how PAM works if you’re unfamiliar with it. Some starting places might be , , and .man 7 PAMman 5 pam.confman 8 pam_unix

An option was added to pam_unix.so (around Feb-2016) called no_pass_expiry (source code change here or man page here). Basically it tells pam_unix to ignore an expired password if something other than pam_unix was used for auth, e.g. if sshd performed the auth.

As a result, if you have a version of pam_unix.so that contains that option, you should be able to configure PAM to:

Still warn but don’t require a change to an expired password if an SSH key was used to authenticate via ssh
Require a password change of an expired password if a login/password via pam_unix.so was used to authenticate via ssh
Not impact any other auth sequence (e.g. via the login service).
For example, I configured a RHEL 7 server to do the above by simply updating /etc/pam.d/sshd and adding pam_unix.so no_pass_expiry to both the account and password types, e.g.

三、解决办法
$ vim /etc/pam.d/sshd
account    required    pam_nologin.so
account    sufficient  pam_unix.so no_pass_expiry
account    include     password-auth
password   sufficient  pam_unix.so no_pass_expiry
password   include     password-auth

四、开通sftp功能，但禁用ssh登录的方法
#修改/etc/passwd中对应的shell为：
owen:x:500:500::/home/owen:/usr/libexec/openssh/sftp-server
```

