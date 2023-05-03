```shell
ntp配置

# Permit all access over the loopback interface.  This could
# be tightened as well, but to do so would effect some of
# the administrative functions.
restrict 127.0.0.1
restrict -6 ::1
restrict 10.150.34.0 mask 255.255.255.0
# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
server  10.150.34.33
server 127.127.1.0
fudge 127.127.1.0 stratum 10
```

