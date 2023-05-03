```shell
修改 10.50.166.21-28 的 /app/bea/BATCH1/WEB-INF/classes/PathConfig.properties 这个文件

ftpAddress = ftp://zscftprs:znscscftprs123@10.50.166.49:21  只修改这两个
RUANSAO_FTP_ZNSC=ftp://zscftprs:znscscftprs123@10.50.166.49:21 只修改这两个

das_ftp_address=ftp://csftpdas:gwssi@10.50.162.67:21     不管 
BAOMI_FTP_NEWEES=ftp://scftpbm:gwssi@10.50.162.100:21/IN  不管
RUANSAO_FTP_EES3=ftp://scftprs3:gwssi@10.50.162.100:21  不管
BAOMI_FTP_EES3=ftp://scftpbm3:gwssi@10.50.162.100:21/IN  不管
WAIGUAN_FTP_EES2=ftp://scftprs:gwssi@10.50.162.100:21  不管
WAIGUAN_FTP_EES3=ftp://scftprs3:gwssi@10.50.162.100:21  不管

znsc_ftp_vs 10.50.166.49 负载地址

znsc_ftp_pool 负载池, 
10.50.166.21
10.50.166.22
10.50.166.23 

ftp密码修改步骤:
1. 修改10.50.166.21-23的 ftp 账号 zscftprs 的密码,
2. 使用负载地址10.50.166.49 在IE浏览器中登录验证一下, 新密码是否可以访问
ftp://zscftprs:lU513O,90jEoLxw@10.50.166.49
3. 修改10.50.166.21-28 的 /app/bea/ BATCH1(服务名) /WEB-INF/classes/PathConfig.properties 这个文件, 这个athConfig.properties 文件修改前, 做好备份
cp -pr /app/bea/BATCH1/WEB-INF/classes/PathConfig.properties{,.20210407}
4. 替换密码
perl -p -i -e 's/    \!U513O\@90jEqCLxw    /    lU513O\,90jEoLxw   /g' /app/bea/BATCH1/WEB-INF/classes/PathConfig.properties
                               旧密码	                             新密码   (特殊字符需要转义)
5. 替换完毕后, 使用grep查看一下, 没问题重启服务验证即可
6. 批量杀掉 java 进程
ps -ef |grep java |grep -v agent |grep 'Dweblogic.Name' |awk '{print $2,$16}'
ps -ef |grep java |grep -v agent |grep 'Dweblogic.Name' |awk '{print $2}'|xargs kill -9
ps -ef |grep java |grep -vi agent |grep -vi AdminServer |grep -v grep |grep 'Dweblogic.Name' |awk '{print $2,$15}'


aix 替换字符串的命令
perl -p -i -e 's/\!U513O\@90jEqCLxw/lU513O\,90jEoLxw/g' /app/bea/BATCH1/WEB-INF/classes/PathConfig.properties
linux替换字符串的命令(整行替换)
sed -i '/^ftpAddress/c \ftpAddress = ftp://zscftprs:!U513O@90jEqCLxw@10.50.166.49:21' /app/bea/BATCH1/WEB-INF/classes/PathConfig.properties
linux替换字符串的命令(部分替换)
sed -i 's/xx/yy/g' xx.txt
```

