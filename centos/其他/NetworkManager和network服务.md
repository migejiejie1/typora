Linux NetworkManager和network服务
1、NetworkManager

     归于GNOME界面管理器管理，在rehat6版本以后推出。支持以界面形式对无线、有限网络进行管理，同时，NetworkManager以后台进程作为server端，可通过nmcli客户端进行配置。
    
     在网卡配置文件中，如配置NM_CONTROLLED=yes , 网卡配置立即生效，原因是NetworkManager后台一直运行，重新读入文件，导致配置生效。该配置项需注意，如配置文件中配置出错，会导致网卡立即报错。


2、network模块

     该模块服务不在后台持续运行，仅在启动时读入网卡配置文件，根据网卡配置文件对网卡进行配置。
    
     如修改网卡配置文件，需要重启该服务才可生效，该服务实际重新读入网卡配置，完成网卡配置后正常退出。