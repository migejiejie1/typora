```
1、pssh 简介
　　PSSH提供OpenSSH和相关工具的并行版本。包括pssh，pscp，prsync，pnuke和pslurp。该项目包括psshlib，可以在自定义应用程序中使用。
pssh是python编写，可以并发在多台机器上批量执行命令的工具，用法可以媲美ansible的一些简单用法，执行起来速度比ansible快，支持文件并行复制，远程命令。
2、pssh安装
3、pssh包的命令介绍   
pssh 在多个主机上并行运行命令
pscp 把文件并行复制到多个主机上
prsync 通过rsync协议把文件高效并行复制到多个主机上
pslurp 把文件并行地从多个远程主机复制到中心主机上
pnuke 并行地在多个远程主机上杀死进程
4、pssh包的命令参数说明及使用
　　为命令执行使用先准备一个主机列表文件，用于连接对应主机执行远程命令
　　内容可以是主机名，也可以直接填写IP, 格式"host[:port] [user]"
　　如：cat /home/pssh/pssh_hosts
　　192.168.1.122
　　xxx.xxx.xxx.xxx
1）pssh命令参数及使用：在多个主机上并行运行命令
　　pssh命令参数：　
pssh Usage: pssh [OPTIONS] -h hosts.txt prog [arg0] ..-h --hosts 主机文件列表，格式"host[:port] [user]"-l --user  用户名-p --par 并发线程数-o --outdir 输出的文件目录-e --errdir 错误输出的文件目录-t --timeout 设置命令执行超时时间 -1表示无限制-O --options 设置ssh的一些选项-v --verbose 详细模式-P --print 打印出输出执行信息-i --inline 在每台host执行完毕后，显示出输出信息Example: pssh -h nodes.txt -l irb2 -o /tmp/pssh uptime
pssh --helpUsage: pssh [OPTIONS] command [...]Options:  --help                show this help message and exit  -h HOST_FILE, --hosts=HOST_FILE                        hosts file (each line "[user@]host[:port]")  -H HOST_STRING, --host=HOST_STRING                        additional host entries ("[user@]host[:port]")  -l USER, --user=USER  username (OPTIONAL)  -p PAR, --par=PAR     max number of parallel threads (OPTIONAL)  -o OUTDIR, --outdir=OUTDIR                        output directory for stdout files (OPTIONAL)  -e ERRDIR, --errdir=ERRDIR                        output directory for stderr files (OPTIONAL)  -t TIMEOUT, --timeout=TIMEOUT                        timeout (secs) (0 = no timeout) per host (OPTIONAL)  -O OPTION, --option=OPTION                        SSH option (OPTIONAL)  -v, --verbose         turn on warning and diagnostic messages (OPTIONAL)  -A, --askpass         Ask for a password (OPTIONAL)  -x ARGS, --extra-args=ARGS                        Extra command-line arguments, with processing for                        spaces, quotes, and backslashes  -X ARG, --extra-arg=ARG                        Extra command-line argument  -i, --inline          inline aggregated output for each server  -I, --send-input      read from standard input and send as input to ssh  -P, --print           print output as we get itExample: pssh -h hosts.txt -l irb2 -o /tmp/foo uptime
　　pssh命令使用
pssh -i -O "StrictHostKeyChecking=no" -h /home/pssh/pssh_hosts 'date'　　  //第一次执行时使用 -O 参数 ,“-O”参数，后面跟的“StrictHostKeyChecking=no”是sshd服务的配置文件ssh_config中的一个选项，通过设置这个参数，可以让远程主机自动接受本地主机的hostkey。第一次执行必须指定，不然报错。
pssh -i -l root -h /home/pssh/pssh_hosts 'date'　　　　//可以指定命令执行用户 -l root
pssh -i -v -p 2 -h /home/pssh/pssh_hosts 'date'　　　　// 指定并发线程数 -p 2, -v 打印详细信息
pssh  -i -o /tmp/pssh.correct -e /tmp/pssh.error -h /home/pssh/pssh_hosts 'cat /etc/passwd |grep root; ls 3304'  　　//使用-o把正确的信息输出到/tmp/pssh.correct目录下的对应以主机名或ip命名的文件中，使用-e把错误的信息输出到/tmp/pssh.error目录下的对应以主机名或ip命名的文件
 2）pscp命令参数及使用：把文件并行复制到多个主机上
　　pscp命令参数
pscp --helpUsage: pscp [OPTIONS] -h hosts.txt local remoteOptions:  --help                show this help message and exit  -h HOST_FILE, --hosts=HOST_FILE                        hosts file (each line "[user@]host[:port]")  -H HOST_STRING, --host=HOST_STRING                        additional host entries ("[user@]host[:port]")  -l USER, --user=USER  username (OPTIONAL)  -p PAR, --par=PAR     max number of parallel threads (OPTIONAL)  -o OUTDIR, --outdir=OUTDIR                        output directory for stdout files (OPTIONAL)  -e ERRDIR, --errdir=ERRDIR                        output directory for stderr files (OPTIONAL)  -t TIMEOUT, --timeout=TIMEOUT                        timeout (secs) (0 = no timeout) per host (OPTIONAL)  -O OPTION, --option=OPTION                        SSH option (OPTIONAL)  -v, --verbose         turn on warning and diagnostic messages (OPTIONAL)  -A, --askpass         Ask for a password (OPTIONAL)  -x ARGS, --extra-args=ARGS                        Extra command-line arguments, with processing for                        spaces, quotes, and backslashes  -X ARG, --extra-arg=ARG                        Extra command-line argument  -r, --recursive       recusively copy directories (OPTIONAL)Example: pscp -h hosts.txt -l irb2 foo.txt /home/irb2/foo.txt
　　pscp命令使用
pscp  -h pssh_hosts -r /home/pssh /tmp/pssh/        //-r递归复制将/home/pssh目录复制到主机的/home/pssh/目录下
3）prsync 命令参数及使用：通过rsync协议把文件高效并行复制到多个主机上
　　prsync命令参数
prsync --helpUsage: prsync [OPTIONS] -h hosts.txt local remoteOptions:  --help                show this help message and exit  -h HOST_FILE, --hosts=HOST_FILE                        hosts file (each line "[user@]host[:port]")  -H HOST_STRING, --host=HOST_STRING                        additional host entries ("[user@]host[:port]")  -l USER, --user=USER  username (OPTIONAL)  -p PAR, --par=PAR     max number of parallel threads (OPTIONAL)  -o OUTDIR, --outdir=OUTDIR                        output directory for stdout files (OPTIONAL)  -e ERRDIR, --errdir=ERRDIR                        output directory for stderr files (OPTIONAL)  -t TIMEOUT, --timeout=TIMEOUT                        timeout (secs) (0 = no timeout) per host (OPTIONAL)  -O OPTION, --option=OPTION                        SSH option (OPTIONAL)  -v, --verbose         turn on warning and diagnostic messages (OPTIONAL)  -A, --askpass         Ask for a password (OPTIONAL)  -x ARGS, --extra-args=ARGS                        Extra command-line arguments, with processing for                        spaces, quotes, and backslashes  -X ARG, --extra-arg=ARG                        Extra command-line argument  -r, --recursive       recusively copy directories (OPTIONAL)  -a, --archive         use rsync -a (archive mode) (OPTIONAL)  -z, --compress        use rsync compression (OPTIONAL)  -S ARGS, --ssh-args=ARGS                        extra arguments forsshExample: prsync -r -h hosts.txt -l irb2 foo /home/irb2/foo
　　prsync命令使用
prsync  -h pssh_hosts -r /home/pssh /tmp/pssh/      //-r递归将/home/pssh传到各主机 /tmp/pssh/目录下
4）pslurp 命令参数及使用：把文件并行地从多个远程主机复制到中心主机上
　　pslurp 命令参数
pslurpUsage: pslurp [OPTIONS] -h hosts.txt remote localpslurp: error: Paths not specified.[root@c43a02001.cloud.a02.amtest1221 /root]#pslurp --helpUsage: pslurp [OPTIONS] -h hosts.txt remote localOptions:  --help                show this help message and exit  -h HOST_FILE, --hosts=HOST_FILE                        hosts file (each line "[user@]host[:port]")  -H HOST_STRING, --host=HOST_STRING                        additional host entries ("[user@]host[:port]")  -l USER, --user=USER  username (OPTIONAL)  -p PAR, --par=PAR     max number of parallel threads (OPTIONAL)  -o OUTDIR, --outdir=OUTDIR                        output directory for stdout files (OPTIONAL)  -e ERRDIR, --errdir=ERRDIR                        output directory for stderr files (OPTIONAL)  -t TIMEOUT, --timeout=TIMEOUT                        timeout (secs) (0 = no timeout) per host (OPTIONAL)  -O OPTION, --option=OPTION                        SSH option (OPTIONAL)  -v, --verbose         turn on warning and diagnostic messages (OPTIONAL)  -A, --askpass         Ask for a password (OPTIONAL)  -x ARGS, --extra-args=ARGS                        Extra command-line arguments, with processing for                        spaces, quotes, and backslashes  -X ARG, --extra-arg=ARG                        Extra command-line argument  -r, --recursive       recusively copy directories (OPTIONAL)  -L LOCALDIR, --localdir=LOCALDIR                        output directory for remote file copiesExample: pslurp -h hosts.txt -L /tmp/outdir -l irb2 /home/irb2/foo.txt foo.txt
　　pslurp 命令使用
pslurp -h /home/pssh/pssh_hosts -L /tmp/pssh -l root /tmp/pssh.test test1   //-L:指定从远程主机拷贝文件放置/tmp/pssh目录下的用远程主机IP或主机名命名的目录下，拷贝/tmp/pssh.test文件，并将其重命名为test1,如果拷贝目录需要使用-r参数
5) pnuke命令参数及使用：并行地在多个远程主机上杀死进程
　　pnuke命令参数
pnuke --helpUsage: pnuke [OPTIONS] -h hosts.txt patternOptions:  --help                show this help message and exit  -h HOST_FILE, --hosts=HOST_FILE                        hosts file (each line "[user@]host[:port]")  -H HOST_STRING, --host=HOST_STRING                        additional host entries ("[user@]host[:port]")  -l USER, --user=USER  username (OPTIONAL)  -p PAR, --par=PAR     max number of parallel threads (OPTIONAL)  -o OUTDIR, --outdir=OUTDIR                        output directory for stdout files (OPTIONAL)  -e ERRDIR, --errdir=ERRDIR                        output directory for stderr files (OPTIONAL)  -t TIMEOUT, --timeout=TIMEOUT                        timeout (secs) (0 = no timeout) per host (OPTIONAL)  -O OPTION, --option=OPTION                        SSH option (OPTIONAL)  -v, --verbose         turn on warning and diagnostic messages (OPTIONAL)  -A, --askpass         Ask for a password (OPTIONAL)  -x ARGS, --extra-args=ARGS                        Extra command-line arguments, with processing for                        spaces, quotes, and backslashes  -X ARG, --extra-arg=ARG                        Extra command-line argumentExample: pnuke -h hosts.txt -l irb2 java
　　pnuke命令使用
pnuke -h /home/pssh/pssh_hosts tail//删除远程主机上tail 进程
```

