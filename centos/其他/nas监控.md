```shell
dir=("/dev" "/dev/shm" "/home")
num1=("a" "b" "c")

for i in {0..2};
do
    num1[$i]=$(df -i | grep ${dir[$i]}$ |awk '{print $3}')
done

sleep 5

for i in {0..2};
do
    tmp=$(expr $(df -i | grep ${dir[$i]}$ |awk '{print $3}') - ${num1[$i]} )
    if [ $tmp -le -10 ];then
        echo "$(date +%Y%m%d:%H:%M:%S)  Warning:" >> /tmp/tmp.log
        echo "NAS: ${dir[$i]} , Reduce inode in 5 seconds: $tmp" >> /tmp/tmp.log
    fi  
done  

===================================================================

[root@zabbix_monitor ~]# cat mon_nas_inode.sh
#!/bin/bash

to="houwenbo@cpms.com.cn"
subject='E system NAS warning: '
log_file=/root/mon_nas.log

function sendmail() {
       /usr/local/bin/sendEmail -f zabbix@cpms.com.cn -t "$to" -s cpms.com.cn -u "$subject"rset=utf8 -xu zabbix@cpms.com.cn -xp Gsip02o19 -m "$body" -o tls=no
}

/usr/bin/ansible -i /etc/ansible/hwb ip -m script -a '/root/mon_nas.sh' &> /dev/null
sleep 30

if ssh root@10.50.167.82 -p 12306 "ls /tmp/mon_nas.log" &> /dev/null; then
    scp -P 12306 root@10.50.167.82:/tmp/mon_nas.log  /root/ &> /dev/null
    body=`cat mon_nas.log`
    sendmail &> /dev/null
    ssh root@10.50.167.82 -p 12306 "cd /tmp && rm -f mon_nas.log"
    cd /root && rm -f mon_nas.log
fi

===================================================================

[root@zabbix_monitor ~]# cat mon_nas_inode.sh.bak 
#!/bin/bash
#监控E系统NAS目录inode是否减少

to="houwenbo@cpms.com.cn"
subject='E系统 /EES/TABLE_TEMPLATE inode 报警'
log_file=/root/mon_nas.log

function sendmail() {
       /usr/local/bin/sendEmail -f zabbix@cpms.com.cn -t "$to" -s cpms.com.cn -u "$subject" -o message-content-type=html -o message-charset=utf8 -xu zabbix@cpms.com.cn -xp Gsip02o19 -m "$body" -o tls=no
}


inodeNum1=$(ssh root@10.50.167.82 -p 12306 "df -i |awk '/EES\/TABLE_TEMPLATE/ {print \$3}'")
sleep 10
inodeNum2=$(ssh root@10.50.167.82 -p 12306 "df -i |awk '/EES\/TABLE_TEMPLATE/ {print \$3}'")  

if  [ $inodeNum2 -lt $inodeNum1 ]; then
    echo "警告: " >> $log_file 
    echo "当前NAS盘的inode数: $inodeNum1" >> $log_file 
    num=$(expr $inodeNum2 - $inodeNum1)
    echo "现在inode数相比与10秒前,减少了: $num" >> $log_file 
fi

if [ -f $log_file ]; then
    body=`cat $log_file`
    sendmail
    cd /root && rm -f mon_nas.log
fi

```

