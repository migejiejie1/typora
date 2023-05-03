```shell
利用pam模块passwdqc限定linux用户密码强度

限定linux上的root密码强度本身就是一个伪命题。root用户可以直接操作/etc/shadow 修改密码。之所以要这么搞主要还是为了稍微提高一下系统的安全性，毕竟不是人人都知道shadow的改法。
windows也类似，可以通过修改注册表的方式修改密码，但是windows的安全策略依然有密码强度的选项。
之所以选择passwdqc，是因为网上找了一下，发现绝大多数的linux密码强度策略限制都只针对非root用户，而passwdqc可以限定到root用户。
passwdqc的主页如下：
http://www.openwall.com/passwdqc/
配置文件的规则说明如下：
http://www.openwall.com/passwdqc/README.shtml

简单找一个我的规则解释一下：
(注：ubuntu的配置文件在/etc/pam.d/common-password redhat和centos的配置文件在/etc/pam.d/sys-auth)
我的规则是这样的：
password required  pam_passwdqc.so min=disabled,disabled,12,8,8 max=30 passphrase=3 match=4 similar=deny enforce=everyone disable_firstupper_lastdigit_chec

规则解释如下：
1）只包含一种字符的密码，拒绝（大小写，数字，特殊字符分别为4种字符）
2）只包含两种字符的密码，拒绝
3）如果密码中被识别出了常用的单词，那么最小长度就必须为12位。常用单词的最小识别长度为3.(由passphrase=3制定)
4）包含三种字符的密码，最小长度为8位
5) 包含四种字符的密码，最小长度为8位
6）如果发现本次修改的密码跟老密码有4个substring的长度类似，就认为是弱密码。（match=4 similar=deny)
7) 这个策略对所有用户都生效。（enforce=everyone ）
8）默认情况下，对于输入的密码，如果第一个字母是大写字母，或者最后一个字母为数字，则不会计入密码字符的种类。比如：Fuck1234，由于第一个F是大写字母，所以会被认为，该密码只有小写字母和数字两种字符。这个非常奇怪，我也不知道原因是什么。我们用disable_firstupper_lastdigit_check 禁止了这种识别。

需要注意的是disable_firstupper_lastdigit_check并不是官方的一个功能，官方的版本发布到1.0.5就结束了，这个功能是在1.0.5-4发布的，是redhat的一个开发人员提交的。
如下图：
[img]http://dl2.iteye.com/upload/attachment/0091/3368/a6c10733-e403-3de4-b0b6-5303738055cc.jpg[/img]

fedora中有一个srpms:ftp://mirrors1.kernel.org/fedora-secondary/releases/15/Everything/source/SRPMS/pam_passwdqc-1.0.5-7.fc15.src.rpm
这个是1.0.5-7版本。
我这边附件提供了一个由此patch出来的pam_passwdqc.so文件。大家需要的可以下载。

有了这样的配置，我们就可以放心修改密码了，如果不符合上述规范，会提示出来的。
[img]http://dl2.iteye.com/upload/attachment/0091/3372/03127333-734a-38c3-a57b-333b1fdcb760.jpg[/img]
```

