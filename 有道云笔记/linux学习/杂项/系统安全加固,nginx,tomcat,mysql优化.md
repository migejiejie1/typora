```shell
Nginx企业级优化
隐藏版本号
a)      修改源码包
[root@nginx ~]# vim nginx-1.14.2/src/core/nginx.h
13 #define NGINX_VERSION      "1.1.1"
14 #define NGINX_VER          "IIS/" NGINX_VERSION
b)     修改配置文件
[root@nginx ~]# vim /usr/local/nginx/conf/nginx.conf
       28     server_tokens off;
修改Nginx用户与组
a)      编译安装时指定
./configure --prefix=/usr/local/nginx --user=nginx --group=nginx
b)     修改配置文件
[root@nginx ~]# vim /usr/local/nginx/conf/nginx.conf
       2 user  nginx nginx;
配置Nginx网页缓存时间
[root@nginx ~]# vim /usr/local/nginx/conf/nginx.conf
……
        location ~ \.(gif|jpg|jpeg|png|bmp|ico)$ {
        expires 1d;
        }
……
Nginx的日志切割
mv /usr/local/nginx/logs/access.log /backup/logs/nginx/access.log-$(date -d "-1 day" +%Y%m%d)
/usr/local/nginx/sbin/nginx -s reopen
gzip -9 /backup/logs/nginx/access.log- date -d "-1 day" +%Y%m%d
find /backup/logs/nginx/ -name "*.log*" -mtime +30 | xargs rm -f
配置Nginx连接超时
[root@nginx ~]# vim /usr/local/nginx/conf/nginx.conf
34     keepalive_timeout  65;
35     client_header_timeout 60;
36     client_body_timeout 60;
更改Nginx运行进程数
[root@nginx ~]# vim /usr/local/nginx/conf/nginx.conf
3 worker_processes  1;
worker_cpu_affinity  0001 0010 0100 1000  //分配不同的进程给不同的CPU处理
配置Nginx网页压缩
[root@nginx ~]# vim /usr/local/nginx/conf/nginx.conf
……
 38     gzip  on;     //开启gzip压缩输出
 39     gzip_min_length 1k;       //用于设置允许压缩的页面最小字节数
 40     gzip_buffers 4 16k;         //表示申请4个单位为16k的内存作为压缩结果流缓存，默认值是申请与原始数据大小相同的内存空间来储存gzip压缩结果
 41     gzip_http_version 1.1;    //设置识别http协议版本，默认是1.1
 42     gzip_comp_level 2;         //gzip压缩比，1-9等级
 43   gzip_types text/plain text/javascript application/x-javascript text/css text/xml application/xml application/xml+rss;             //压缩类型，是就对哪些网页文档启用压缩功能
 44     #gzip_vary  on;       //选项可以让前端的缓存服务器经过gzip压缩的页面
……
配置Nginx防盗链功能
[root@nginx1 ~]# vim /usr/local/nginx/conf/nginx.conf
……
        location ~* \.(jpg|gif|png|swf)$ {
               root /;
            valid_referers none blocked *.amber.com amber.com;
            if ($invalid_referer) {
                rewrite ^/ http://www.amber.com/error.jpg;
                #return 403;
            }
        }
……
对FPM模块进行参数优化
# vim /usr/local/php5/etc/php-fpm.conf
优化参数调整：
pm = dynamic     //static和dynamic，前者将产生固定数量的fpm进程，后缀将以动态的方式产生fpm进程
pm_start_servers = 5    //动态方式下初始的fpm进程数量
pm.min_spare_servers = 2   //动态方式下最小的fpm空闲进程数
pm.max_spare_servers = 8   //动态方式下最大的fpm空闲进程数
 
MySQL数据库优化
优化mysql数据库的方法：
建立Index索引，少用select *语句，开启查询缓存，选择适合的存储引擎，避免在where子句中使用or来连接以及避免大数据量返回等
一、Index索引
主键索引
二、少用select *
三、explain select
explain显示了mysql如何使用索引来处理select语句以及连接表。主要用法就是在select前加上explain即可。
四、开启查询缓存
第一步把query_cache_type设置为ON，然后查询系统变量have_query_cache是否可用：show variables like 'have_query_cache'
之后，分配内存大小给查询缓存，控制缓存查询结果的最大值。相关操作在配置文件中进行修改。
五、使用not null
最好指定列为 NOT NULL
六、存储引擎的选择
如果是一些小型的应用或项目，那么MyISAM也许会更适合
如果你正在计划使用一个超大数据量的项目，而且需要事务处理或外键支持，那么你真的应该直接使用InnoDB方式。
七、避免在 where 子句中使用 or 来连接
如果一个字段有索引，一个字段没有索引，将导致引擎放弃使用索引而进行全表扫描
select id from t where num = 10
union all           
select id from t where Name = 'admin'
union     //此操作将使您可以将多个数据集合并为一个数据集，并删除所有存在的重复项。基本上，它在结果集中的所有列上执行DISTINCT操作。 
union all  //再次执行此操作，您可以将多个数据集连接到一个数据集中，但不会删除任何重复的行。因为这不会删除重复的行，所以此过程更快，但是如果您不希望重复的记录，则需要使用UNION运算符。
UNION数据规则
每个查询必须具有相同数量的列
每列必须具有兼容的数据类型
最终结果集的列名取自第一个查询
ORDER BY和COMPUTE子句只能针对整个结果集发出，而不能在每个单独的结果集中发出
GROUP BY和HAVING子句只能为每个单独的结果集发出，而不能为整体结果集发出
八、多使用varchar/nvarchar
可以节省存储空间
九、避免大数据量返回
考虑使用limit，来限制返回的数据量，如果每次返回大量自己不需要的数据，也会降低查询速度。
十、where子句优化
应尽量避免在 where 子句中对字段进行表达式操作，避免在where子句中对字段进行函数操作这将导致引擎放弃使用索引而进行全表扫描。不要在 where 子句中的“=”左边进行函数、算术运算或其他表达式运算，否则系统将可能无法正确使用索引。
 
Tomcat和JVM调优
Tomcat调优
增加连接数
调整工作模式
启用gzip压缩
调整JVM内存大小
作为web服务器时，与Apache或者Nginx整合
合理选择垃圾回收算法
尽量使用较新的jdk
 
代码片段：
<Connectorport="8080"protocol="org.apache.coyote.http11.Http11NioProtocol"
               maxThreads="1000"
               minSpareThreads="100"
               maxSpareThreads="200"
               acceptCount="900"
               disableUploadTimeout="true"
               connectionTimeout="20000"
               URIEncoding="UTF-8"
               enableLookups="false"
               redirectPort="8443"
               compression="on"
               compressionMinSize="1024"
               compressableMimeType="text/html,text/xml,text/css,text/javascript"
/>
 
参数解读：
org.apache.coyote.http11.Http11NioProtocol：调整工作模式为Nio
maxThreads：最大线程数，默认150。增大值避免队列请求过多，导致响应缓慢。
minSpareThreads：最小空闲线程数。
maxSpareThreads：最大空闲线程数，如果超过这个值，会关闭无用的线程。
acceptCount：当处理请求超过此值时，将后来请求放到队列中等待。
disableUploadTimeout：禁用上传超时时间
connectionTimeout：连接超时，单位毫秒，0代表不限制
URIEncoding：URI地址编码使用UTF-8
enableLookups：关闭dns解析，提高响应时间
compression：启用压缩功能
compressionMinSize：最小压缩大小，单位Byte
compressableMimeType：压缩的文件类型
 
JVM调优
了解了JVM基础知识，下面配置下相关Java参数，将下面一段放到catalina.sh里面：
JAVA_OPTS="-server -Xms1024m -Xmx1536m -XX:PermSize=256m -XX:MaxPermSize=512m   -XX:+UseConcMarkSweepGC -XX:+UseParallelGCThreads=8                             -XX:CMSInitiatingOccupancyFraction=80 -XX:+UseCMSCompactAtFullCollection           -XX:CMSFullGCsBeforeCompaction=0 -XX:-PrintGC -XX:-PrintGCDetails              -XX:-PrintGCTimeStamps -Xloggc:../logs/gc.log"
 
参数解读：

```

| **参数**                               | **描述**                                                     |
| -------------------------------------- | ------------------------------------------------------------ |
| -Xms                                   | 堆内存初始大小，单位m、g                                     |
| -Xmx                                   | 堆内存最大允许大小，一般不要大于物理内存的80%                |
| -XX:PermSize                           | 非堆内存初始大小，一般应用设置初始化200m，最大1024m就够了    |
| -XX:MaxPermSize                        | 非堆内存最大允许大小                                         |
| -XX:+UseParallelGCThreads=8            | 并行收集器线程数，同时有多少个线程进行垃圾回收，一般与CPU数量相等 |
| -XX:+UseParallelOldGC                  | 指定老年代为并行收集                                         |
| -XX:+UseConcMarkSweepGC                | CMS收集器（并发收集器）                                      |
| -XX:+UseCMSCompactAtFullCollection     | 开启内存空间压缩和整理，防止过多内存碎片                     |
| -XX:CMSFullGCsBeforeCompaction=0       | 表示多少次Full GC后开始压缩和整理，0表示每次Full GC后立即执行压缩和整理 |
| -XX:CMSInitiatingOccupancyFraction=80% | 表示老年代内存空间使用80%时开始执行CMS收集，防止过多的Full GC |

**注意：JVM的内存不是设置越大越好，具体还要根据项目对象实际占用内存大小而定的。**

```shell
Linux系统安全加固
系统无用账号清理
a)      将非登录用户解释器设为/sbin/nologin
b)     锁定长期不使用的账号
c)      删除无用的账号
d)     锁定账号文件(+i锁)   // chattr +/-i /etc/passwd  /etc/shadow
密码安全控制
a)      设置密码的有效期
                 i.          chage -M 天数 user
               ii.          passwd -x 天数 user
b)     设置密码的有效期
                 i.          vim /etc/login.defs
PASS_MAX_DAY 90
c)      设置用户下次登录时修改密码
chage -d 0 user
passwd -e user
d)     历史命令限制
                 i.          减少历史的命令条数
vim /etc/profile
HISTSIZE=100
export HISTSIZE=100
               ii.          注销自动清空历史命令
vim .bash.logout
history -c
e)     终端自动注销
                 i.          vim /etc/profile
TMOUT=600
               ii.          export TMOUT=600
切换用户 su 命令
禁止用户随意su到root用户
sudo授权
a)      单个用户授权
                 i.          vim  /etc/sudoers 或visudo
user host=command
b)     批量授权
wheel组
c)      查看sudo操作
vim  /etc/sudoers 或visudo
Defaults logfile=/var/log/sudo
PAM安全认证
开关机安全控制
a)      BIOS设置
                 i.          设置硬盘为第一引导
               ii.          禁用其他引导设备
              iii.          设置管理员密码
b)     禁用重启热键Ctrl+Alt+Del
c)      gurb菜单加密
终端登录安全控制
a)      减少开放终端个数
vim /etc/init/start-ttys.conf  /etc/sysconfig/init
b)     限制root只在安全终端登录
vim /etc/securetty
c)      禁止普通用户登录
touch /etc/nologin   //系统检测该文件存在,则普通用户禁止登录
弱口令检测
端口检测 NMAP
关闭无用的端口
禁止root用户远程登陆
系统用户使用密钥进行登录
登录IP地址限制
vim /etc/hosts.allow
sshd:192.168.1.0

```

