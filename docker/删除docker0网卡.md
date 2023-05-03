**卸载docker后，删除docker0网卡**

```shell
# ifconfig docker0 down
# yum -y install bridge-utils
# brctl delbr docker0
```

