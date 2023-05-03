```shell
Usage:
    jstack [-l] <pid>
        (to connect to running process)  (连接正在运行的进程)
    jstack -F [-m] [-l] <pid>
        (to connect to a hung process)   (连接到挂起的进程)
    jstack [-m] [-l] <executable> <core>
        (to connect to a core file)    (连接到核心文件)
    jstack [-m] [-l] [server_id@]<remote server IP or hostname>
        (to connect to a remote debug server)  (连接到远程调试服务器)

Options:
    -F  to force a thread dump. Use when jstack <pid> does not respond (process is hung)
    -F强制线程转储。当jstack &lt;没有响应(进程挂起)

    -m  to print both java and native frames (mixed mode)
    -m打印java和本机帧(混合模式)

    -l  long listing. Prints additional information about locks
    - l长清单。打印关于锁的附加信息

    -h or -help to print this help message
    -h或-help以打印此帮助消息
```

