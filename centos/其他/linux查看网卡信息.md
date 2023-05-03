ethtool就可以看到link是no,那么表示是没有连线的

Linux中有ethtool命令可以查看网卡状态。比如网卡是eth0，那么： ethtool eth0会有很多输出，查看Speed：那一行，如果是“Unknown!”那么表示是没有连线的