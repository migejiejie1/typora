Centos6 配置最新yum源

 centos6操作系统版本已经结束了生命周期(EOL)，linux社区已经不再维护该操作系统的版本
  建议升级操作系统至centos7 
  如果生产中业务过度期仍需要使用centos6系统中的一些安装包，请按照下文切换centos6的yum源

  2020年11月30日CentOS 6 EOL。按照社区规则
        CentOS 6的源地址 http://mirror.centos.org/centos-6/内容已移除
        目前第三方的镜像站中均已移除CentOS 6的源。
        阿里云的源 http://mirrors.cloud.aliyuncs.com和 http://mirrors.aliyun.com也无法同步到CentOS 6的源。
  当您在阿里云上继续使用默认配置的CentOS 6的源会发生报错。报错示例如下图所示：

http://mirrors.aliyun.com/centos/6/os/x86_64/repodata/repomd.xml: [Errno 14] PYCURL ERROR 22 - "The requested URL returned error: 404 Not Found"
Trying other mirror.

解决办法:
  直接在linux命令行执行以下代码即可

```shell
cat >> /etc/yum.repos.d/CentOS-aliyun-lhr.repo <<"EOF"
[base]
name=CentOS-6.10
enabled=1
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos-vault/6.10/os/$basearch/
gpgcheck=1
gpgkey=http://mirrors.aliyun.com/centos-vault/RPM-GPG-KEY-CentOS-6
[updates]
name=CentOS-6.10
enabled=1
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos-vault/6.10/updates/$basearch/
gpgcheck=1
gpgkey=http://mirrors.aliyun.comm/centos-vault/RPM-GPG-KEY-CentOS-6
[extras]
name=CentOS-6.10
enabled=1
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos-vault/6.10/extras/$basearch/
gpgcheck=1
gpgkey=http://mirrors.aliyun.com/centos-vault/RPM-GPG-KEY-CentOS-6
[epel]
name=Extra Packages for Enterprise Linux 6 - $basearch
enabled=1
failovermethod=priority
baseurl=http://mirrors.aliyun.com/epel-archive/6/$basearch
gpgcheck=0
gpgkey=http://mirrors.aliyun.com/epel-archive/RPM-GPG-KEY-EPEL-6
EOF

cat >> /etc/yum.repos.d/CentOS-neusoft-lhr.repo <<"EOF"
[base]
name=CentOS-6-Base
baseurl=http://mirrors.neusoft.edu.cn/centos/6/os/$basearch/
gpgcheck=1
gpgkey=http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-6
[updates]
name=CentOS-6-Updates
baseurl=http://mirrors.neusoft.edu.cn/centos/6/updates/$basearch/
gpgcheck=1
gpgkey=http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-6
[extras]
name=CentOS-6-Extras
baseurl=http://mirrors.neusoft.edu.cn/centos/6/extras/$basearch/
gpgcheck=1
gpgkey=http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-6
[centosplus]
name=CentOS-6-Plus
baseurl=http://mirrors.neusoft.edu.cn/centos/6/centosplus/$basearch/
gpgcheck=1
enabled=0
gpgkey=http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-6
[contrib]
name=CentOS-6-Contrib
baseurl=http://mirrors.neusoft.edu.cn/centos/6/contrib/$basearch/
gpgcheck=1
enabled=0
gpgkey=http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-6
[epel]
name=Extra Packages for Enterprise Linux 6 - $basearch
enabled=1
failovermethod=priority
baseurl=http://mirrors.neusoft.edu.cn/epel/6/$basearch
gpgcheck=0
gpgkey=http://mirrors.neusoft.edu.cn/epel/RPM-GPG-KEY-EPEL-6
EOF
```

