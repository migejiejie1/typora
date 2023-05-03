生产weblogic启停脚本

bash-4.2$  vi sc1.sh     //启动脚本

```
#!/bin/sh
rm -rf /app/bea/bea103/user_projects/domains/znscDomain/servers/SCXZ_1/tmp
export USER_MEM_ARGS="-Xms1024m -Xmx4096m -DServerName=znscDomain  -Dhost.ip=192.1
68.7.121 -verbose:gc -Xverbosegclog:gc-server`date +%m%d%H%M`.log"
/app/bea/managed.sh start SCXZ_1 http://localhost:7001
```

bash-4.2$ vi sc1_stop.sh           //停止脚本

```
#!/bin/sh
/app/bea/managed.sh stop SCXZ_1
```

bash-4.2$ vi managed.sh                   //管理脚本

```
#!/bin/sh
OPT_=$1
case "$OPT_" in

start)
#export LANG=zh_CN.GB18030
/bin/echo "delete log files"
/bin/echo "$0 : (start)"
cd /app/bea/bea103/user_projects/domains/znscDomain/bin/
./startManagedWebLogic.sh $2 $3 > /app/bea/logs/$2.run`date +%m%d%H%M`.log &
;;

stop)
/bin/echo "$0 : (stop)"
cd /app/bea/bea103/user_projects/domains/znscDomain/bin/
./stopManagedWebLogic.sh $2 $3 &
;;

esac
exit 0
```

灾备weblogic启停脚本

weblogic@ww-cpees-yj3:~> vim yj1.sh             //启动脚本

```
export USER_MEM_ARGS="-Xms1024m -Xmx2048m -XX:PermSize=256m -XX:MaxPermSize=512m -DServerName=yjDomain"
managed.sh start DZSQYJ1 http://localhost:9080
```

weblogic@ww-cpees-yj3:~> vim yj1_stop.sh                 //停止脚本

```
managed.sh stop DZSQYJ1
```

weblogic@ww-cpees-yj3:~> vim managed.sh                       //管理脚本

```
#!/bin/sh
OPT_=$1
case "$OPT_" in

start)
export LANG=zh_CN.GB18030
/bin/echo "delete log files"
/bin/echo "$0 : (start)"
cd /app/bea/bea100/user_projects/domains/yjDomain/bin/
./startManagedWebLogic.sh $2 $3 > /app/bea/logs/$2.run`date +%m%d%H%M`.log &
;;

stop)
/bin/echo "$0 : (stop)"
cd /app/bea/bea100/user_projects/domains/yjDomain/bin/
./stopManagedWebLogic.sh $2 $3 &
;;

esac
exit 0
```

