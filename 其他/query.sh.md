```shell
#!/bin/bash
time=`date`
hostip=`ssh root@10.50.13.194 "hostname -I"`


sendmail(){
    to="houwenbo@cpms.com.cn"
    #to="xiongjun@cpms.com.cn"
    #subject=`echo -n $2 | base64`
    subject="=?utf-8?b?$subject?="
    subject=`echo $subject | tr '\r\n' '\n'`
    #body=`echo $3 | tr '\r\n' '\n'`
    /usr/local/bin/sendEmail -f zabbix@cpms.com.cn -t "$to" -s cpms.com.cn -u "$subject" -o message-content-type=html -o message-charset=utf8 -xu zabbix@cpms.com.cn -xp Gsip02o19 -m "$body" -o tls=no
}

query(){
    CONSUMER=`ssh gbase@10.50.13.194 'gccli -ugbase -pGbase@2018!  -e "select * from information_schema.kafka_consumer_status;"'|sed -n '2p'|awk '{print $1}'`
    IP=`ssh gbase@10.50.13.194 'gccli -ugbase -pGbase@2018!  -e "select * from information_schema.kafka_consumer_status;"'|sed -n '2p'|awk '{print $2}'`
    TOPIC=`ssh gbase@10.50.13.194 'gccli -ugbase -pGbase@2018!  -e "select * from information_schema.kafka_consumer_status;"'|sed -n '2p'|awk '{print $3}'`
    STATUS=`ssh gbase@10.50.13.194 'gccli -ugbase -pGbase@2018!  -e "select * from information_schema.kafka_consumer_status;"'|sed -n '2p'|awk '{print $4}'`
    MIN_OFFSET=`ssh gbase@10.50.13.194 'gccli -ugbase -pGbase@2018!  -e "select * from information_schema.kafka_consumer_status;"'|sed -n '2p'|awk '{print $5}'`
    MAX_OFFSET=`ssh gbase@10.50.13.194 'gccli -ugbase -pGbase@2018!  -e "select * from information_schema.kafka_consumer_status;"'|sed -n '2p'|awk '{print $6}'`
    CUR_OFFSET=`ssh gbase@10.50.13.194 'gccli -ugbase -pGbase@2018!  -e "select * from information_schema.kafka_consumer_status;"'|sed -n '2p'|awk '{print $7}'`
    PROCESS_OFFSET=`ssh gbase@10.50.13.194 'gccli -ugbase -pGbase@2018!  -e "select * from information_schema.kafka_consumer_status;"'|sed -n '2p'|awk '{print $8}'`
    COMMIT_OFFSET=`ssh gbase@10.50.13.194 'gccli -ugbase -pGbase@2018!  -e "select * from information_schema.kafka_consumer_status;"'|sed -n '2p'|awk '{print $9}'`
    OP_TS=`ssh gbase@10.50.13.194 'gccli -ugbase -pGbase@2018!  -e "select * from information_schema.kafka_consumer_status;"'|sed -n '2p'|awk '{print $10" "$11}'`
    EXCEPTION=`ssh gbase@10.50.13.194 'gccli -ugbase -pGbase@2018!  -e "select * from information_schema.kafka_consumer_status;"'|sed -n '2p'|awk '{print $(NF+1)}'`
    echo $time $hostip Gbase数据库查询成功 > /tmp/query.log
    if [[ -f new_index.html ]];then
        mv new_index.html old_index.html
    fi
  		cat <<-EOF > new_index.html
		<style>
		table{border-collapse:collapse;border-spacing:0;border-left:1px solid #000;border-top:1px solid #000;border:solid 1px #000;color:#000}
		th,td{border-right:1px solid #000;border-bottom:1px solid #000;border:solid 1px #000;padding:3px;font-size:12px;}
		th{font-weight:bold;}
		</style>

		<table border="1">
		  <tr>
			<th>CONSUMER</th>
			<th>IP</th>
			<th>TOPIC</th>
			<th>STATUS</th>
			<th>MIN_OFFSET</th>
			<th>MAX_OFFSET</th>
			<th>CUR_OFFSET</th>
			<th>PROCESS_OFFSET</th>
			<th>COMMIT_OFFSET</th>
			<th>OP_TS</th>
			<th>EXCEPTION</th>
		  </tr>
		  <tr>
			<td>$CONSUMER</td>
			<td>$IP</td>
			<td>$TOPIC</td>
			<td>$STATUS</td>
			<td>$MIN_OFFSET</td>
			<td>$MAX_OFFSET</td>
			<td>$CUR_OFFSET</td>
			<td>$PROCESS_OFFSET</td>
			<td>$COMMIT_OFFSET</td>
			<td>$OP_TS</td>
			<td>$EXCEPTION</td>
		  </tr>
		</table>
		EOF
    if [[ $(diff new_index.html old_index.html) != "" ]];then
        subject=`echo -n Gbase数据库同步正常 | base64`
        body=`echo -e "$time<br><br>$hostip Gbase数据库查询结果:<br><br>select * from information_schema.kafka_consumer_status;<br><br>";cat old_index.html;echo "<br>";cat new_index.html`
        #sendmail
    else
        subject=`echo -n Gbase数据库同步异常 | base64`
        body=`echo -e "$time<br><br>$hostip Gbase数据库查询结果:<br><br>select * from information_schema.kafka_consumer_status;<br><br>";cat old_index.html;echo "<br>";cat new_index.html`
        sendmail
    fi
    
}

query

```

