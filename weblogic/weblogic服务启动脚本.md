admin.sh 

```shell
[root@localhost /]# cat admin.sh 
#!/bin/sh
OPT_=$1
case "$OPT_" in

start)
export LANG=en_US.UTF-8
/bin/echo "$0 : (start)"
/bin/echo "delete log files"

cd /app/bea/bea103/user_projects/domains/cpeesDomain/bin
./startWebLogic.sh > /app/bea/logs/AdminServer/Admin.run`date +%m%d%H%M`.log &
;;

stop)
/bin/echo "$0 : (stop)"

cd /app/bea/bea103/user_projects/domains/cpeesDomain/bin
./stopWebLogic.sh &
;;

*)
/bin/echo ''
/bin/echo "Usage: $0 [start|stop]"
/bin/echo "Invalid argument ==>; \"${OPT_}\""
/bin/echo ''
;;

esac
exit 0 
```

managed.sh 

```shell
[root@localhost /]# cat  /app/bea/scripts/managed.sh 
#!/bin/sh
OPT_=$1
case "$OPT_" in

start)
export LANG=zh_CN.UTF-8
/bin/echo "delete log files"
/bin/echo "$0 : (start)"

cd /app/bea/bea121/user_projects/domains/newwgjsdomain/bin

WL_HOME=/app/bea/bea103/wlserver_10.3/
export WL_HOME

PATCH_CLASSPATH=${WL_HOME}/server/ext/jdbc/oracle/11g/ojdbc5.jar:${WL_HOME}/server/ext/jdbc/oracle/11g/orai18n.jar:${WL_HOME}/server/ext/jdbc/oracle/11g/orai18n-mapping.jar:${PATCH_CLASSPATH}
export PATCH_CLASSPATH

WEBLOGIC_CLASSPATH=${PATCH_CLASSPATH}:${CLASSPATHSEP}:${WEBLOGIC_CLASSPATH}
export WEBLOGIC_CLASSPATH

./startManagedWebLogic.sh $2 $3 > /app/bea/logs/$2.run`date +%m%d%H%M`.log &
;;

stop)
/bin/echo "$0 : (stop)"

cd /app/bea/bea121/user_projects/domains/newwgjsdomain/bin
./stopManagedWebLogic.sh $2 $3 &
;;

esac
exit 0 
```

sc.sh

```shell
[root@localhost /]# cat sc.sh
#!/bin/sh
wlsname=CPEES

echo "清理烽火台缓存"
rm -rf /app/bea/logs/$wlsname/data/*
mv /app/bea/logs/$wlsname/$wlsname/freeze.diagnosis.log /app/bea/logs/$wlsname/$wlsname/freeze.diagnosis.log_`date +%m%d%H%M`

echo "清理weblogic缓存"
rm -rf /app/bea/bea103/user_projects/domains/cpeesDomain/servers/$wlsname/tmp/*

echo "设置运行环境"
export USER_MEM_ARGS="-Xms8192m -Xmx8192m -XX:PermSize=256m -XX:MaxPermSize=512m -DServerName=cpeesDomain -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/app/bea/dump -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -Xloggc:/app/bea/dump/gc-$wlsname`date +%m%d%H%M`.log"
#export USER_MEM_ARGS="-Xms1024m -Xmx14288m  -XX:PermSize=256m -XX:MaxPermSize=512m -DServerName=wgjsDomain -Xss512k -XX:-UseSplitVerifier  -Duser.timezone=GMT+8 -Dfile.encoding=utf-8 -Djava.security.egd=file:/dev/./urandom -Xrs"

echo "启动服务"
/app/bea/scripts/managed.sh start $wlsname http://localhost:9080
```

sc_stop.sh 

```shell
[root@localhost /]# cat sc_stop.sh 
#!/bin/sh
wlsname=CPEES
./managed.sh stop $wlsname
```

