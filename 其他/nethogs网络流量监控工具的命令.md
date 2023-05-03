```shell
nethogs
相关命令：暂无相关命令
NetHogs是一款开源、免费的，终端下的网络流量监控工具，它可监控Linux的进程或应用程序的网络流量。NetHogs只能实时监控进程的网络带宽占用情况。NetHogs支持IPv4和IPv6协议，支持本地网卡以及PPP链接。


NetHogs提供交互式控制指令：

	m : Cycle between display modes (kb/s, kb, b, mb) 切换网速显示单位

	r : Sort by received. 按接收流量排序

	s : Sort by sent. 按发送流量排序

	q : Quit and return to the shell prompt. 退出NetHogs命令工具




常用的参数：

	-d delay for refresh rate. 数据刷新时间 如nethogs -d 1 就是每秒刷新一次

	-h display available commands usage. 显示命名帮助、使用信息

	-p sniff in promiscious mode (not recommended).

	-t tracemode.
	-V prints Version info.


# nethogs -d 5  # 5秒刷新一次数据

# cnethogs eth0  # 监控网卡eth0数据

# nethogs eth0 eth1  #同时监视eth0和eth1接口

# nethogs >>test.log  #将监控日志写入日志文件
```

