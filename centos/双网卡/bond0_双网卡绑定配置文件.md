```shell
ifcfg-bond0

MACADDR=9c:b6:54:9b:59:c4    ///////修改/////////
USERCTL=no
PERSISTENT_DHCLIENT=1
BONDING_MASTER=yes
NM_CONTROLLED=no
BOOTPROTO=static
BONDING_OPTS="mode=1 miimon=100"
DEVICE=bond0
DHCPV6C=yes
TYPE=Bond
ONBOOT=yes
IPADDR=172.21.3.231                 ///////修改/////////
NETMASK=255.255.255.192      ///////修改/////////
GATEWAY=172.21.3.254            ///////修改/////////

===============================
ifcfg-eth0

USERCTL=no
MTU=8888
NM_CONTROLLED=no
BOOTPROTO=dhcp
DEVICE=eth0                          ///////修改/////////
TYPE=Ethernet
ONBOOT=yes
MASTER=bond0
SLAVE=yes

ifcfg-eth1

USERCTL=no
MTU=8888
NM_CONTROLLED=no
BOOTPROTO=dhcp
DEVICE=eth1                        ///////修改/////////
TYPE=Ethernet
ONBOOT=yes
MASTER=bond0
SLAVE=yes
```

