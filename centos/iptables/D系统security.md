```shell
#! /bin/sh

#SSH访问相关
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
iptables -A INPUT -p tcp --dport 12306 -j ACCEPT

#ICMP（？？作用）
iptables -A INPUT -p icmp -j ACCEPT
#网段内部访问策略
iptables -A INPUT -s 10.51.17.0/24 -p tcp -j ACCEPT
#NAS相关（云NAS）
iptables -A INPUT -s 10.160.19.0/24 -p tcp -j ACCEPT
#NAS相关（老NAS）
iptables -A INPUT -s 10.51.14.13 -p tcp -j ACCEPT
iptables -A INPUT -s 10.51.14.9 -p tcp -j ACCEPT
iptables -A INPUT -s 10.52.22.3 -p tcp -j ACCEPT
#ORACLE交互
iptables -A INPUT -s 10.51.18.1 -p tcp -j ACCEPT

#监控IP
iptables -A INPUT -s 10.50.128.159 -p tcp --dport 10012 -j ACCEPT
#自定义列表（按需添加）
iptables -A INPUT -s 10.75.12.249 -p tcp --dport 10012 -j ACCEPT
#.......

iptables -I INPUT 1 -s 10.50.128.0/24  -p tcp --dport 12001 -j ACCEPT

iptables -I INPUT 1 -s 10.50.128.24  -p tcp --dport 12001 -j ACCEPT
iptables -I INPUT 1 -s 10.50.128.66  -p tcp --dport 12001 -j ACCEPT
iptables -I INPUT 1 -s 10.50.128.69  -p tcp --dport 12001 -j ACCEPT
iptables -I INPUT 1 -s 10.50.128.75  -p tcp --dport 12001 -j ACCEPT
iptables -I INPUT 1 -s 10.50.128.18  -p tcp --dport 12001 -j ACCEPT

iptables -I INPUT 1 -s 10.75.12.149  -p tcp --dport 12001 -j ACCEPT
iptables -I OUTPUT 1 -s 0.0.0.0/0  -p tcp --dport 12001 -j ACCEPT

iptables -D INPUT 1
iptables -D OUTPUT 1

#严格策略执行
iptables -P INPUT DROP

#策略保存
service iptables save
#策略自动启动设置
chkconfig iptables on
#显示最后的策略
iptables -L -n

echo "add security complete!"


```

