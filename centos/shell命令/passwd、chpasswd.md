```shell
Linux命令之passwd、chpasswd

(1).命令passwd

passwd [-k] [-l] [-u [-f]] [-d] [-e] [-n mindays] [-x maxdays] [-w warndays] [-i inactivedays] [-S] [--stdin] [username]

　　passwd程序用于更新用户的身份验证令牌（口令/密码）。此任务是通过调用Linux-PAM和Libuser API实现的。实际上，它将自身初始化为Linux-PAM的”passwd”服务，并利用配置的密码模块进行身份验证，然后更新用户的密码。

1）选项
-k,--keep 此选项仅用于更新过期的身份验证令牌（口令/密码）；用户希望保留没有过期的身份验证令牌（口令/密码）
-l,--lock 此选项用于锁定指定账户的密码，仅适用于root用户。通过将加密密码呈现为无效字符串（通过在加密字符串前加上！）来执行锁定。
　　注意:该账户未完全锁定——用户仍可通过其他身份验证方式登录，例如ssh公钥身份验证。使用”chage –E 0 user（这里面是零）”命令代替完全账户锁定
--stdin 此选项用于指示passwd应从标准输入读取新密码，标准输入可以是管道（|）
-u,--unlock 这与-l选项相反——它通过删除字首！来解锁账户密码。一样仅适应于root用户。默认情况下，passwd将拒绝创建无密码账户（它不会解锁只有！作为密码的账户）。强制选项-f将废除此保护。
-d,--delete 这是删除账户密码的快捷方式。它将指定账户设置为无密码，仅适用于root用户。
-e,--expire 这是一个过期账户密码的快捷方式。在下次尝试登录期间，用户将被迫更改密码。仅适用于root用户。
-f,--force 强制指定的操作。
-n,--minimum DAYS 如果用户的账户支持密码生存期，这将设置最小密码生存期（单位天），仅适用于root用户。
-x,--maximum DAYS 如果用户的账户支持密码生存期，这将设置最长密码生存期（单位天），仅适用于root用户。
-w,--warning DAYS 如果用户的账户支持密码生存期，这将设置用户其密码将过期前DAYS天开始警告，仅适用于root用户。
-i,--inactive DAYS如果用户的账户支持密码生存期，这将设置此账户密码过期前经过的天数，这意味着账户将被视为不活动且应禁用，仅适用于root用户。
-S,--status 这将输出有关于给定账户的密码状态的简短信息，仅适用于root用户。

2).命令chpasswd

chapasswd [选项]

　　批量更新密码。注意：命令内没有用户名和密码，回车后以"用户名:密码"的格式输入（密码一般为明文），chpasswd根据选项加密

1)常用选项
-c,--crypt-method METHOD 使用指定的方法加密。加密方法有DES，MD5，NONE，SHA256，SHA512
-e,--encrypted 提供的密码已经加密
-h,--help 帮助
-m.--md5

(3).实例：非交互式修改密码

echo 123456 | passwd --stdin user002
echo "user003:123456" | chpasswd
```

