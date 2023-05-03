```shell
1. 查看是那块nas出问题了 
ps aux |grep df  

1、通过strace df -h追踪是卡在什么位置
strace df -h

2. 查看挂载的盘对应的nas的IP与目录
mount |grep /xxx 

2. 使用 mount |column -t 查看所有的挂载信息
mount |column -t 

3. 卸载掉问题盘符 , 如卸不掉, 可强制卸载 -f
umount /xxx

4. 重新挂载问题盘符
mount 192.168.16.35:/vx/wwnas35   /xxx

5. df |grep /xxx确认一下
```

