```shell
echo "exclude=kernel*" >> /etc/yum.conf
echo "exclude=centos-release*" >> /etc/yum.conf

tail /etc/yum.conf

yum update -y

```

