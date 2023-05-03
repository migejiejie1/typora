**SSH本地端口转发: **

```shell
ssh -L [LOCAL_IP:]LOCAL_PORT:DESTINATION:DESTINATION_PORT [USER@]SSH_SERVER
示例:
ssh -L 127.0.0.1:5901:127.0.0.1:5901 root@192.168.1.8 -p 12306 -N -f
-f 命令在后台运行, -N 不是执行远程命令
```

这里 DESTINATION 写 localhost 因为 VNC 和 SSH 服务器在同一主机上运行。

**SSH远程端口转发:**

```shell
ssh -R [REMOTE:]REMOTE_PORT:DESTINATION:DESTINATION_PORT [USER@]SSH_SERVER
ssh -R 8080:127.0.0.1:3000 -N -f user@remote.host
```

**SSH动态端口转发:**

```shell
ssh -D [LOCAL_IP:]LOCAL_PORT [USER@]SSH_SERVER
ssh -D 9090 -N -f user@remote.host
```

