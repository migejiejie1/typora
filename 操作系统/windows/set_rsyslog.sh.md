```shell
#!/bin/bash
usage(){
    if [ $# == 0 ];then
        echo "usage: $0 [set|unset]"
        exit 1
    fi
}

set_rsyslog(){
    \cp /etc/{profile,profile.bak}
cat << EOF >> /etc/profile

#history
HISTFILESIZE=2000
HISTSIZE=2000
DT=\`date +"%Y-%m-%d %H:%M:%S"\`
LHOST=\`hostname -I\`
RHOST=\`echo \$SSH_CLIENT|awk '{print \$1}'\`
export PROMPT_COMMAND='{ command=\$(history 1 | { read x y; echo \$y; }); logger -p local5.notice -t bash -i "time=\$DT,user=\$USER,lhost=\$LHOST,rhost=\$RHOST,cmd=\$command"; }'
EOF
    
    \cp /etc/rsyslog{.conf,.conf.bak}
cat << EOF >> /etc/rsyslog.conf
#将history记录到/var/log/history.log文件中
local5.* /var/log/history.log
#历史命令记录日志发送到10.50.168.11的528端口(udp)
local5.*  @10.50.168.11:528
#安全日志发送到10.50.168.11的530端口(udp)
authpriv.*  @10.50.168.11:530
#计划任务日志发送到10.50.168.11的532端口(udp)
cron.*  @10.50.168.11:532
##将所有日志发送到10.50.168.11的534端口(udp)
#*.* @10.50.168.11:534
EOF
    
    source /etc/profile
    systemctl restart rsyslog.service
}

unset_rsyslog(){
    \mv /etc/{profile.bak,profile}
    \mv /etc/rsyslog{.conf.bak,.conf}
    rm -rf /var/log/history.log
}


case $1 in
    set)
        set_rsyslog
    ;;
    unset)
        unset_rsyslog
    ;;
    *)
        usage
esac
```

