```shell

vrrp_script chk_rabbit {

script 'sh /etc/keepalived/chk_rabbit'

interval 2

weight 15

}

vrrp_script chk_nginx {

script 'sh /etc/keepalived/chk_nginx'

interval 2

weight 15

}

vrrp_instance MYSQL_HA1 {

state MASTER

interface bond1

virtual_router_id 12

priority 110

advert_int 1

authentication {

auth_type PASS

auth_pass 23236

}

virtual_ipaddress {

10.25.23.236 label bond1:MYSQL_HA1

}

track_script {

chk_mysql

}

# smtp_alert

notify_master '/etc/keepalived/send_mail.sh master 10.25.23.236'

notify_backup '/etc/keepalived/send_mail.sh backup 10.25.23.236'

notify_fault '/etc/keepalived/send_mail.sh fault 10.25.23.236'

}

vrrp_instance MYSQL_HA2 {

state MASTER

interface bond1

virtual_router_id 13

priority 120

advert_int 1

authentication {

auth_type PASS

auth_pass 23236

}

virtual_ipaddress {

10.25.23.237 label bond1:MYSQL_HA2

}

track_script {

chk_mysql

}

}

############################################################

vrrp_instance RABBIT_HA1 {

state MASTER

interface bond1

virtual_router_id 14

priority 160

advert_int 1

authentication {

auth_type PASS

auth_pass 23236

}

virtual_ipaddress {

10.25.23.234 label bond1:RABBIT1

}

track_script {

chk_rabbit

}

# smtp_alert

notify_master '/etc/keepalived/send_mail.sh master 10.25.23.234'

notify_backup '/etc/keepalived/send_mail.sh backup 10.25.23.234'

notify_fault '/etc/keepalived/send_mail.sh fault 10.25.23.234'

}

vrrp_instance RABBIT_HA2 {

state MASTER

interface bond1

virtual_router_id 15

priority 150

advert_int 1

authentication {

auth_type PASS

auth_pass 23236

}

virtual_ipaddress {

10.25.23.235 label bond1:RABBIT2

}

track_script {

chk_rabbit

}

}

############################################################

vrrp_instance NGINX_HA1 {

state MASTER

interface bond0

virtual_router_id 16

priority 160

advert_int 1

authentication {

auth_type PASS

auth_pass 23236

}

virtual_ipaddress {

11.25.46.23 label bond0:NGINX1

}

track_script {

chk_nginx

}

# smtp_alert

notify_master '/etc/keepalived/send_mail.sh master 11.25.46.23'

notify_backup '/etc/keepalived/send_mail.sh backup 11.25.46.23'

notify_fault '/etc/keepalived/send_mail.sh fault 11.25.46.23'

}

vrrp_instance NGINX_HA2 {

state MASTER

interface bond0

virtual_router_id 17

priority 150

advert_int 1

authentication {

auth_type PASS

auth_pass 23236

}

virtual_ipaddress {

11.25.46.86 label bond0:NGINX2

}

track_script {

chk_nginx

}

}

# 以下是 3台服务器做 HA 3台 服务器需将 优先级错开

# keepalived configure

global_defs {

router_id HA2

}

############################### VRRP-SCRIPT ####################################

vrrp_script chk_redis {

script 'sh /etc/keepalived/redis.sh'

interval 2

weight 25

}

############################### REDIS-HA ########################################

vrrp_instance REDIS_HA1 {

state BACKUP

interface eth0

virtual_router_id 50

priority 100

advert_int 1

authentication {

auth_type PASS

auth_pass 1234

}

virtual_ipaddress {

10.23.23.230 label eth0:REDIS_HA1

}

track_script {

chk_redis

}

}

vrrp_instance REDIS_HA2 {

state BACKUP

interface eth0

virtual_router_id 51

priority 110

advert_int 1

authentication {

auth_type PASS

auth_pass 1234

}

virtual_ipaddress {

10.23.23.231 label eth0:REDIS_HA2

}

track_script {

chk_redis

}

}

vrrp_instance REDIS_HA3 {

state BACKUP

interface eth0

virtual_router_id 52

priority 120

advert_int 1

authentication {

auth_type PASS

auth_pass 1234

}

virtual_ipaddress {

10.23.23.232 label eth0:REDIS_HA3

}

track_script {

chk_redis

}

}

keepalived 调试

tcpdump -i bond0 vrrp 224.0.0.0/8
```

