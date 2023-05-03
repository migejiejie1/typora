```shell
#echo 4194303 > /proc/sys/kernel/pid_max
echo "kernel.pid_max=4194303 " >> /etc/sysctl.conf ; sysctl -p
cat /proc/sys/kernel/pid_max
grep 'kernel.pid_max' /etc/sysctl.conf

```

