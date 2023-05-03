```shell
sendmail(){
    to="houwenbo@cpms.com.cn"
    #subject=`echo -n $2 | base64`
    subject="=?utf-8?b?$subject?="
    subject=`echo $subject | tr '\r\n' '\n'`
    #body=`echo $3 | tr '\r\n' '\n'`
    
    /usr/local/bin/sendEmail \
    -f zabbix@cpms.com.cn \          //发件人邮箱
    -t "$to" \			     //收件人邮箱
    -s cpms.com.cn \                 //发件人的smtp服务器地址
    -u "$subject" \                  //邮件标题
    -o message-content-type=html \   //邮件内容格式为html
    -o message-charset=utf8 \        //邮件内容编码为utf-8 
    -xu zabbix@cpms.com.cn \         //发件人邮箱登录用户名
    -xp Gsip02o19 \                  //发件人邮箱登录密码
    -m "$body" \                     //邮件内容
    -o tls=no
}
```

