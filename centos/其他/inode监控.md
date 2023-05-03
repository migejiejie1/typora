```shell
#!/bin/bash
#监控E系统NAS目录inode是否减少

log_file=/tmp/mon_nas.log

inodeNum1=$(ssh root@10.50.167.82 -p 12306 "df -i |awk '/EES\/TABLE_TEMPLATE/ {print \$3}'")
sleep 60
inodeNum2=$(ssh root@10.50.167.82 -p 12306 "df -i |awk '/EES\/TABLE_TEMPLATE/ {print \$3}'")  

if  [ $inodeNum2 -lt $inodeNum1 ]; then
    echo "警告: " >> $log_file 
    echo "当前NAS盘的inode数: $inodeNum1" >> $log_file 
    num=`expr $inodeNum2 - $inodeNum1`
    echo "现在inode数相比与60秒前,减少了: $num" >> $log_file 
fi

if [ -f $log_file ]; then
    sendmail(){
	    to="houwenbo@cpms.com.cn"
	    #subject=`echo -n $2 | base64`
	    subject="=?utf-8?b?$subject?="
	    subject=`echo $subject | tr '\r\n' '\n'`
	    body=`cat $log_file`
	    
	    /usr/local/bin/sendEmail -f zabbix@cpms.com.cn -t "$to" -s cpms.com.cn -u "$subject" -o message-content-type=html -o message-charset=utf8 -xu zabbix@cpms.com.cn -xp Gsip02o19 -m "$body" -o tls=no
     }
     cd /tmp && rm -f mon_nas.log
fi






#!/bin/bash
#监控E系统NAS目录inode是否减少

to="houwenbo@cpms.com.cn"
subject='E系统 /EES/TABLE_TEMPLATE inode 报警'
log_file=/root/mon_nas.log

function sendmail() {
       /usr/local/bin/sendEmail -f zabbix@cpms.com.cn -t "$to" -s cpms.com.cn -u "$subject"rset=utf8 -xu zabbix@cpms.com.cn -xp Gsip02o19 -m "$body" -o tls=no
}


inodeNum1=$(ssh root@10.50.167.82 -p 12306 "df -i |awk '/EES\/TABLE_TEMPLATE/ {print \$3}'"
sleep 60
inodeNum2=$(ssh root@10.50.167.82 -p 12306 "df -i |awk '/EES\/TABLE_TEMPLATE/ {print \$3}'"

if  [ $inodeNum2 -lt $inodeNum1 ]; then
    echo "警告: " >> $log_file 
    echo "当前NAS盘的inode数: $inodeNum1" >> $log_file 
    num=$(expr $inodeNum2 - $inodeNum1)
    echo "现在inode数相比与60秒前,减少了: $num" >> $log_file 
fi

if [ -f $log_file ]; then
    body=`cat $log_file`
    sendmail
    cd /root && rm -f mon_nas.log
fi


```

