```shell
supervisor
supervisor管理进程，是通过fork/exec的方式将这些被管理的进程当作supervisor的子进程来启动，所以我们只需要将要管理进程的可执行文件的路径添加到supervisor的配置文件中就好了。此时被管理进程被视为supervisor的子进程，若该子进程异常中断，则父进程可以准确的获取子进程异常中断的信息，通过在配置文件中设置autostart=ture，可以实现对异常中断的子进程的自动重启。
安装supervisor
$ sudo apt-get install supervisor
配置文件
安装完supervisor后，输入以下命令可得到配置文件：
$ echo_supervisord_conf
或者：
$ cat /etc/supervisord/supervisord.conf
配置文件如下（分号；表示注释）：
; supervisor config file[unix_http_server]file=/var/run/supervisor.sock   
;(the path to the socket file)chmod=0700; sockef file mode(default 0700)[supervisord]logfile=/var/log/supervisor/supervisord.log 
;(main log file;default $CWD/supervisord.log)pidfile=/var/run/supervisord.pid 
;(supervisord pidfile;default supervisord.pid)childlogdir=/var/log/supervisor            
;('AUTO' child log dir, default $TEMP); the below section must remain in the config file for RPC
;(supervisorctl/web interface)to work, additional interfaces may be
; added by defining them in separate rpcinterface: sections[rpcinterface:supervisor]supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface[supervisorctl]serverurl=unix:///var/run/supervisor.sock 
; use a unix:// URL  for a unix socket; The [include] section can just contain the "files" setting.  This; setting can list multiple files(separated by whitespace or; newlines).  It can also contain wildcards.  The filenames are; interpreted as relative tothis file.  Included files *cannot*
; include files themselves.[include]files =/etc/supervisor/conf.d/*.conf
以上配置文件用到几个部分：
[unix_http_server]：这部分设置HTTP服务器监听的UNIX domain socket
file: 指向UNIX domain socket，即file=/var/run/supervisor.sock
chmod：启动时改变supervisor.sock的权限
[supervisord]：与supervisord有关的全局配置需要在这部分设置
logfile: 指向记录supervisord进程的log文件
pidfile：pidfile保存子进程的路径
childlogdir：子进程log目录设为AUTO的log目录
[supervisorctl]：
serverurl：进入supervisord的URL， 对于UNIX domain sockets, 应设为 unix:///absolute/path/to/file.sock
[include]：如果配置文件包含该部分，则该部分必须包含一个files键：
files：包含一个或多个文件，这里包含了/etc/supervisor/conf.d/目录下所有的.conf文件，可以在该目录下增加我们自己的配置文件，在该配置文件中增加[program:x]部分，用来运行我们自己的程序，如下：
[program:x]：配置文件必须包括至少一个program，x是program名称，必须写上，不能为空
command：包含一个命令，当这个program启动时执行
directory：执行子进程时supervisord暂时切换到该目录
user：账户名
startsecs：进程从STARING状态转换到RUNNING状态program所需要保持运行的时间（单位：秒）
redirect_stderr：如果是true，则进程的stderr输出被发送回其stdout文件描述符上的supervisord
stdout_logfile：将进程stdout输出到指定文件
stdout_logfile_maxbytes：stdout_logfile指定日志文件最大字节数，默认为50MB，可以加KB、MB或GB等单位
stdout_logfile_backups：要保存的stdout_logfile备份的数量
示例如下，在目录/etc/supervisor/conf.d/下创建awesome.conf，并加入：
；/etc/supervisor/conf.d/awesome.conf[program:awesome]command     =/usr/bin/env python3 /srv/awesome/www/app.pydirectory   =/srv/awesome/wwwuser        = www-datastartsecs   =3redirect_stderr         =truestdout_logfile_maxbytes =50MBstdout_logfile_backups  =10stdout_logfile          =/srv/awesome/log/app.log
配置完后，先进入/srv/awesome/目录下创建log目录，之后启动supervisor：
$ sudo supervisord -c supervisor.conf  
supervisor基本命令（后四个命令可以省略“-c supervisor.conf”）：
supervisord -c supervisor.conf                       通过配置文件启动supervisorsupervisorctl -c supervisor.conf status              查看状态supervisorctl -c supervisor.conf reload              重新载入配置文件supervisorctl -c supervisor.conf start [all]|[x]     启动所有/指定的程序进程supervisorctl -c supervisor.conf stop [all]|[x]      关闭所有/指定的程序进程 
执行服务（运行app.py）：
$ sudo supervisorctl start awesome
如果supervisor遇到错误，可以在/var/log/supervisor/supervisord.log中查看日志；
如果app运行出现问题，可以在/srv/awesome/log/app.log中查看日志。
	
```

