```shell
实验环境：
CentOS 7.6
MySQL 5.7
MHA 0.58

主机名                IP                        MySQL角色        MHA角色
mysql-node1    192.168.80.101    master                node
mysql-node2    192.168.80.102    slave                   node
mysql-node3    192.168.80.103    slave                   manager

---------------------------------------------------------------------------------------------------

MHA官方文档：
https://github.com/yoshinorim/mha4mysql-manager/wiki

MHA github地址： 
https://github.com/yoshinorim/mha4mysql-manager
https://github.com/yoshinorim/mha4mysql-node


1.在所有节点安装 mha4mysql-node

[root@mysql-node1 ~]# yum install perl-DBD-MySQL -y
[root@mysql-node1 ~]# rpm -ivh mha4mysql-node-0.58-0.el7.centos.noarch.rpm
准备中...                          ################################# [100%]
正在升级/安装...
   1:mha4mysql-node-0.58-0.el7.centos ################################# [100%]

2.在node3节点上安装mha4mysql-manager

[root@mysql-node3 ~]# yum install epel-release -y
[root@mysql-node3 ~]# yum install perl-DBD-MySQL perl-Config-Tiny perl-Log-Dispatch perl-Parallel-ForkManager -y
[root@mysql-node3 ~]# rpm -ivh mha4mysql-manager-0.58-0.el7.centos.noarch.rpm 
准备中...                          ################################# [100%]
正在升级/安装...
   1:mha4mysql-manager-0.58-0.el7.cent################################# [100%]

3.在三个节点上创建ssh秘钥，并设置互信（略）
    注意：每个节点需要给自己也发送公钥

4.在每个数据库中创建监控用户

mysql> grant all privileges on *.* to 'root'@'192.168.80.%' identified by  '123123';
mysql> flush privileges;

5.配置MHA

（1）编写应用程序配置文件 /etc/app1.cnf
    在github上，作者有提供配置文件模板作为参考


 
配置文件参数详解：
https://github.com/yoshinorim/mha4mysql-manager/wiki/Parameters

根据需求,下载官网提供的脚本




[root@mysql-node3 ~]# wget https://raw.githubusercontent.com/yoshinorim/mha4mysql-manager/master/samples/scripts/master_ip_failover

[root@mysql-node3 ~]# wget https://raw.githubusercontent.com/yoshinorim/mha4mysql-manager/master/samples/scripts/send_report

[root@mysql-node3 ~]# mkdir -p /script/masterha/
[root@mysql-node3 ~]# cp master_ip_failover send_report /script/masterha/
[root@mysql-node3 ~]# chmod +x /script/masterha/*


[root@mysql-node3 ~]# cat /etc/app1.cnf
[server default]
# mysql user and password
user=root
password=123123
# working directory on the manager
manager_workdir=/var/log/masterha/app1
# manager log file
manager_log=/var/log/masterha/app1/app1.log
# working directory on MySQL servers
remote_workdir=/var/log/masterha/app1
ping_interval=3
master_ip_failover_script=/script/masterha/master_ip_failover
# shutdown_script= /script/masterha/power_manager
# report_script= /script/masterha/send_report
[server1]
hostname=mysql-node1
[server2]
hostname=mysql-node2
[server3]
hostname=mysql-node3
注：
    1>需要修改hosts文件或有DNS解析
    2>除日志目录外的目录，无需创建，启动服务会自动生成

[root@mysql-node3 ~]# mkdir -p /var/log/masterha/app1

测试：
[root@mysql-node3 ~]# masterha_check_ssh --conf /etc/app1.cnf
[root@mysql-node3 ~]# masterha_check_repl --conf /etc/app1.cnf

撰写绑定VIP脚本
[root@mysql-node3 ~]# cat /script/masterha/master_ip_failover
#!/usr/bin/env perl
use strict;
use warnings FATAL =>'all';
use Getopt::Long;
my (
$command,          $ssh_user,        $orig_master_host, $orig_master_ip,
$orig_master_port, $new_master_host, $new_master_ip,    $new_master_port
);
my $vip = '192.168.80.66/24';  # Virtual IP
my $key = "1";
my $ssh_start_vip = "/sbin/ifconfig ens32:$key $vip";
my $ssh_stop_vip = "/sbin/ifconfig ens32:$key down";
my $exit_code = 0;
GetOptions(
'command=s'          => \$command,
'ssh_user=s'         => \$ssh_user,
'orig_master_host=s' => \$orig_master_host,
'orig_master_ip=s'   => \$orig_master_ip,
'orig_master_port=i' => \$orig_master_port,
'new_master_host=s'  => \$new_master_host,
'new_master_ip=s'    => \$new_master_ip,
'new_master_port=i'  => \$new_master_port,
);
exit &main();
sub main {
#print "\n\nIN SCRIPT TEST====$ssh_stop_vip==$ssh_start_vip===\n\n";
if ( $command eq "stop" || $command eq "stopssh" ) {
        # $orig_master_host, $orig_master_ip, $orig_master_port are passed.
        # If you manage master ip address at global catalog database,
        # invalidate orig_master_ip here.
        my $exit_code = 1;
        eval {
            print "\n\n\n***************************************************************\n";
            print "Disabling the VIP - $vip on old master: $orig_master_host\n";
            print "***************************************************************\n\n\n\n";
&stop_vip();
            $exit_code = 0;
        };
        if ($@) {
            warn "Got Error: $@\n";
            exit $exit_code;
        }
        exit $exit_code;
}
elsif ( $command eq "start" ) {
        # all arguments are passed.
        # If you manage master ip address at global catalog database,
        # activate new_master_ip here.
        # You can also grant write access (create user, set read_only=0, etc) here.
my $exit_code = 10;
        eval {
            print "\n\n\n***************************************************************\n";
            print "Enabling the VIP - $vip on new master: $new_master_host \n";
            print "***************************************************************\n\n\n\n";
&start_vip();
            $exit_code = 0;
        };
        if ($@) {
            warn $@;
            exit $exit_code;
        }
        exit $exit_code;
}
elsif ( $command eq "status" ) {
        print "Checking the Status of the script.. OK \n";
        `ssh $ssh_user\@$orig_master_host \" $ssh_start_vip \"`;
        exit 0;
}
else {
&usage();
        exit 1;
}
}
# A simple system call that enable the VIP on the new master
sub start_vip() {
`ssh $ssh_user\@$new_master_host \" $ssh_start_vip \"`;
}
# A simple system call that disable the VIP on the old_master
sub stop_vip() {
`ssh $ssh_user\@$orig_master_host \" $ssh_stop_vip \"`;
}
sub usage {
print
"Usage: master_ip_failover –command=start|stop|stopssh|status –orig_master_host=host –orig_master_ip=ip –orig_master_port=po
rt –new_master_host=host –new_master_ip=ip –new_master_port=port\n";
}

启动MHA
# nohup masterha_manager --conf=/etc/app1.cnf --ignore_last_failover --ignore_fail_on_start < /dev/null > /var/log/masterha/app1/app1.log 2>&1

撰写发送邮件报警的perl脚本
[root@mysql-node3 ~]# cat /script/masterha/send_report
#!/usr/bin/env perl
use strict;
use warnings FATAL => 'all';
use Email::Simple;
use Email::Sender::Simple qw(sendmail);
use Email::Sender::Transport::SMTP::TLS;
use Getopt::Long;
#new_master_host and new_slave_hosts are set only when recovering master succeeded
my ( $dead_master_host, $new_master_host, $new_slave_hosts, $subject, $body );
GetOptions(
  'orig_master_host=s' => \$dead_master_host,
  'new_master_host=s'  => \$new_master_host,
  'new_slave_hosts=s'  => \$new_slave_hosts,
  'subject=s'          => \$subject,
  'body=s'             => \$body,
);
#mailToContacts($smtp,$mail_from,$mail_user,$mail_pass,$mail_to,$subject,$body);
mailToContacts();
sub mailToContacts {
#    my ( $smtp, $mail_from, $user, $passwd, $mail_to, $subject, $msg ) = @_;
    open my $DEBUG, "> /tmp/monitormail.log"
        or die "Can't open the debug      file:$!\n";
        my $transport = Email::Sender::Transport::SMTP::TLS->new(
                host     => 'smtp.126.com',
                port     => 25,
                username => 'liuyingshan727@126.com',
                password => '用户授权密码',
                );
        my $message = Email::Simple->create(
    header => [
        From           => 'liuyingshan727@126.com',
        To             => 'liuyingshan727@126.com',
        Subject        => $subject,
        ],
        body           =>$body,
);
sendmail( $message, {transport => $transport} );
    return 1;
}
# Do whatever you want here
exit 0;

给perl脚本可执行权限
[root@mysql-node3 ~]# chmod +x /script/masterha/*

补充：
若遇到没有装对应perl模块，可以使用CPAN进行安装
 CPAN（Comprehensive Perl Archive Network）中译为“Perl综合典藏网”，“Perl综合档案网”或者“Perl程序库”。它包含了极多用Perl写成的软件和其文件。CPAN亦是一支Perl程序的名字，其作用是让使用者容易从CPAN下载、安装、更新及管理其他在CPAN上的Perl程式。
[root@mysql-node3 ~]# yum install cpan -y
[root@mysql-node3 ~]# cpan install Mail::Sender


--------------------------------------------------------------------------------
修复后再次启动时，要记得删除failover文件
[root@mysql-node3 ~]# rm -f /var/log/masterha/app1/app1.failover.complete


测试后若想要恢复从节点的复制：
STOP SLAVE;
RESET MASTER;
CHANGE MASTER TO
MASTER_HOST='192.168.80.101',
MASTER_USER='repl',
MASTER_PASSWORD='123123',
MASTER_PORT=3306,
MASTER_AUTO_POSITION = 1;

START SLAVE;

SHOW SLAVE STATUS \G




```

