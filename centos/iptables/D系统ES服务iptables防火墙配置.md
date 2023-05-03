ES服务iptables防火墙配置

```shell
D系统

#ICMP协议开启
iptables -A INPUT -s 0.0.0.0/0 -d 10.160.19.31 -p icmp -j ACCEPT

#远程运维端口
iptables -A INPUT -s 10.160.19.61  -d 10.160.19.31 -p tcp --dport 12306 -j ACCEPT
iptables -A INPUT -s 10.75.16.0/24 -d 10.160.19.31 -p tcp --dport 12306 -j ACCEPT
iptables -A INPUT -s 10.75.12.0/24 -d 10.160.19.31 -p tcp --dport 12306 -j ACCEPT
#阿里运维
iptables -A INPUT -s 10.128.12.173 -d 10.160.19.35 -p tcp --dport 12306 -j ACCEPT
#VPN
iptables -A INPUT -s 10.75.14.125 -d 10.160.19.31 -p tcp --dport 12306 -j ACCEPT
#堡垒机登录
iptables -A INPUT -m iprange --src-range 10.50.167.54-10.50.167.58 -d 10.160.19.35 -p tcp --dport 12306 -j ACCEPT
iptables -A INPUT -s 10.75.13.203 -m iprange --dst-range 10.160.19.31-10.160.19.35 -p tcp --dport 12306 -j ACCEPT


#开放ES集群访问
iptables -A INPUT -m iprange --src-range 10.160.19.32-10.160.19.35 -d 10.160.19.31 -p tcp -j ACCEPT

#开放D系统网段访问ES集群
iptables -A INPUT -s 10.160.19.0/24 -d 10.160.19.31 -p tcp -m multiport --dport 9200,9300 -j ACCEPT

#集中监控地址放行
iptables -A INPUT -s 10.50.128.18 -d 10.160.19.31 -p tcp -m multiport --dport 9200,9300 -j ACCEPT
iptables -A INPUT -s 10.50.128.24 -d 10.160.19.31 -p tcp -m multiport --dport 9200,9300 -j ACCEPT
iptables -A INPUT -s 10.50.128.66 -d 10.160.19.31 -p tcp -m multiport --dport 9200,9300 -j ACCEPT
iptables -A INPUT -s 10.50.128.69 -d 10.160.19.31 -p tcp -m multiport --dport 9200,9300 -j ACCEPT
iptables -A INPUT -s 10.50.128.75 -d 10.160.19.31 -p tcp -m multiport --dport 9200,9300 -j ACCEPT

#外网翻译服务和ES通信
iptables -A INPUT -m iprange --src-range 10.160.129.21-10.160.129.24 -d 10.160.19.31 -p tcp -m multiport --dport 9200,9300 -j ACCEPT

#应用测试访ES服务
iptables -A INPUT -s 10.75.12.249 -d 10.160.19.35 -p tcp --dport 9200 -j ACCEPT
iptables -A INPUT -s 10.75.12.172 -d 10.160.19.35 -p tcp --dport 9200 -j ACCEPT
iptables -A INPUT -s 10.75.12.119 -d 10.160.19.35 -p tcp --dport 9200 -j ACCEPT
iptables -A INPUT -s 10.75.12.175 -d 10.160.19.35 -p tcp --dport 9200 -j ACCEPT
iptables -A INPUT -s 10.75.12.189 -d 10.160.19.35 -p tcp --dport 9200 -j ACCEPT

iptables -A INPUT -s 10.75.12.249 -d 10.160.19.35 -p tcp --dport 9300 -j ACCEPT
iptables -A INPUT -s 10.75.12.172 -d 10.160.19.35 -p tcp --dport 9300 -j ACCEPT
iptables -A INPUT -s 10.75.12.119 -d 10.160.19.35 -p tcp --dport 9300 -j ACCEPT
iptables -A INPUT -s 10.75.12.175 -d 10.160.19.35 -p tcp --dport 9300 -j ACCEPT
iptables -A INPUT -s 10.75.12.189 -d 10.160.19.35 -p tcp --dport 9300 -j ACCEPT

#测试数据库访问ES
iptables -A INPUT -s 10.160.19.46 -d 10.160.19.35 -p tcp -m multiport --dport 9200,9300 -j ACCEPT

#dns服务器
iptables -A INPUT -s 10.160.19.31  -d 10.162.1.1 -p tcp --dport 53 -j ACCEPT
iptables -A INPUT -s 10.160.19.31  -d 10.162.1.2 -p tcp --dport 53 -j ACCEPT
iptables -A INPUT -s 10.160.19.31  -d 10.162.1.1 -p udp --dport 53 -j ACCEPT
iptables -A INPUT -s 10.160.19.31  -d 10.162.1.2 -p udp --dport 53 -j ACCEPT

#添加一条入站规则：对进来的包的状态进行检测。已经建立tcp连接的包以及该连接相关的包允许通过！RELATED 是主动派生的包的意思
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

#安骑士服务端
iptables -A INPUT -s 10.162.4.61 -d 10.160.19.35 -p tcp -j ACCEPT
iptables -A INPUT -s 10.162.4.57 -d 10.160.19.35 -p tcp -j ACCEPT

#默认拒绝所有访问请求
iptables -P INPUT DROP

#时间同步
iptables -A INPUT -s 10.160.19.21  -d 10.160.19.31 -p udp --dport 123 -j ACCEPT

#保存iptables配置,设置开机自启
/etc/init.d/iptables save
chkconfig iptables on

==============================================================================================
#默认允许所有
iptables -P INPUT ACCEPT
#默认拒绝所有访问请求
iptables -P INPUT DROP

#360杀毒软件
iptables -A INPUT -s 10.50.4.153 -p tcp -j ACCEPT

#NAS相关（云NAS）
iptables -A INPUT -m iprange --src-range 10.162.1.1-10.162.1.2 -p tcp -j ACCEPT
iptables -A INPUT -m iprange --src-range 10.162.1.1-10.162.1.2 -p udp -j ACCEPT
#centos7 iptables策略保存
iptables-save > /etc/sysconfig/iptables 
#centos7 iptables策略恢复
iptables-restore /etc/sysconfig/iptables 

==============================================================================================
大数据日志平台ES (新)

#icmp
iptables -A INPUT -s 0.0.0.0/0 -m iprange --dst-range 10.50.168.99-10.50.168.103 -p icmp -j ACCEPT
#远程运维端口
iptables -A INPUT -s 10.75.16.0/24 -m iprange --dst-range 10.50.168.99-10.50.168.103 -p tcp --dport 12306 -j ACCEPT
iptables -A INPUT -s 10.75.12.0/24 -m iprange --dst-range 10.50.168.99-10.50.168.103 -p tcp --dport 12306 -j ACCEPT
#kibana访问界面
iptables -A INPUT -s 10.75.16.0/24 -d 10.50.168.99 -p tcp --dport 5601 -j ACCEPT
iptables -A INPUT -s 10.75.12.0/24 -d 10.50.168.99 -p tcp --dport 5601 -j ACCEPT
iptables -A INPUT -s 10.75.16.0/24 -d 10.50.168.103 -p tcp --dport 5601 -j ACCEPT
iptables -A INPUT -s 10.75.12.0/24 -d 10.50.168.103 -p tcp --dport 5601 -j ACCEPT
#堡垒机登录
iptables -A INPUT -m iprange --src-range 10.50.167.54-10.50.167.58 --dst-range 10.50.168.99-10.50.168.103 -p tcp --dport 12306 -j ACCEPT
#ES集群互访
iptables -A INPUT -m iprange --src-range 10.50.168.99-10.50.168.103 --dst-range 10.50.168.99-10.50.168.103 -p tcp -j ACCEPT
#VPN
iptables -A INPUT -s 10.75.14.125 -m iprange --dst-range 10.50.168.99-10.50.168.103 -p tcp --dport 12306 -j ACCEPT
#
iptables -A INPUT -s 10.50.168.11 -m iprange --dst-range 10.50.168.99-10.50.168.103 -p tcp -j ACCEPT
iptables -A INPUT -s 10.50.4.153 -m iprange --dst-range 10.50.168.99-10.50.168.103 -p tcp -j ACCEPT
iptables -A INPUT -s 10.50.128.69 -m iprange --dst-range 10.50.168.99-10.50.168.103 -p tcp -j ACCEPT
#
iptables -A INPUT -s 10.50.168.182 -m iprange --dst-range 10.50.168.99-10.50.168.103 -p tcp --dport 9201 -j ACCEPT
iptables -A INPUT -s 10.160.19.40  -m iprange --dst-range 10.50.168.99-10.50.168.103 -p tcp --dport 9201 -j ACCEPT
iptables -A INPUT -s 10.50.167.48  -m iprange --dst-range 10.50.168.99-10.50.168.103 -p tcp --dport 9201 -j ACCEPT
iptables -A INPUT -s 10.50.167.60  -m iprange --dst-range 10.50.168.99-10.50.168.103 -p tcp --dport 9201 -j ACCEPT
iptables -A INPUT -s 10.50.168.136 -m iprange --dst-range 10.50.168.99-10.50.168.103 -p tcp --dport 9201 -j ACCEPT
iptables -A INPUT -s 10.50.169.72  -m iprange --dst-range 10.50.168.99-10.50.168.103 -p tcp --dport 9201 -j ACCEPT

iptables -A INPUT -s 10.50.167.82  -d 10.50.168.100 -p tcp --dport 9201 -j ACCEPT

#centos7 iptables策略保存
iptables-save > /etc/sysconfig/iptables 
#centos7 iptables策略恢复
iptables-restore /etc/sysconfig/iptables 

==============================================================================================
大数据日志平台ES (老)

#icmp
iptables -A INPUT -s 0.0.0.0/0 -m iprange --dst-range 10.50.168.136-10.50.168.139 -p icmp -j ACCEPT
#远程运维端口
iptables -A INPUT -s 10.75.16.0/24 -m iprange --dst-range 10.50.168.136-10.50.168.139 -p tcp --dport 12306 -j ACCEPT
iptables -A INPUT -s 10.75.12.0/24 -m iprange --dst-range 10.50.168.136-10.50.168.139 -p tcp --dport 12306 -j ACCEPT
#访问kibana
iptables -A INPUT -s 10.75.16.0/24 -d 10.50.168.136 -p tcp --dport 5601 -j ACCEPT
iptables -A INPUT -s 10.75.12.0/24 -d 10.50.168.136 -p tcp --dport 5601 -j ACCEPT
#VPN
iptables -A INPUT -s 10.75.14.125 -m iprange --dst-range 10.50.168.136-10.50.168.139 -p tcp --dport 12306 -j ACCEPT
#ES集群互访
iptables -A INPUT -m iprange --src-range 10.50.168.136-10.50.168.139 --dst-range 10.50.168.136-10.50.168.139 -p tcp -j ACCEPT
#360防火墙
iptables -A INPUT -s 10.50.4.153   -d 10.50.168.136 -p tcp -j ACCEPT
#
iptables -A INPUT -s 10.50.168.137 -d 10.50.168.136 -p tcp -j ACCEPT
iptables -A INPUT -s 10.50.168.99  -d 10.50.168.136 -p tcp -j ACCEPT
iptables -A INPUT -s 10.50.3.38    -d 10.50.168.136 -p tcp -j ACCEPT
iptables -A INPUT -s 10.50.166.31  -d 10.50.168.136 -p tcp -j ACCEPT
iptables -A INPUT -s 10.50.166.30  -d 10.50.168.136 -p tcp -j ACCEPT
iptables -A INPUT -s 10.50.162.53  -d 10.50.168.136 -p tcp -j ACCEPT

#centos7 iptables策略保存
iptables-save > /etc/sysconfig/iptables 
#centos7 iptables策略恢复
iptables-restore /etc/sysconfig/iptables 

```

