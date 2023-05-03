```shell
zookeeper

zoo.cfg
tickTime=2000

initLimit=5

syncLimit=5

dataDir=/data/zk_data

clientPort=12181

#maxClientCnxns=60

autopurge.snapRetainCount=3

autopurge.purgeInterval=1
server.1=zk-1:12888:13888
server.2=zk-2:12888:13888
server.3=zk-3:12888:13888


java.env
#!/bin/sh

export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.282.b08-1.el7_9.x86_64/jre

export JVMFLAGS="-Xms4096m -Xmx4096m $JVMFLAGS"
```

