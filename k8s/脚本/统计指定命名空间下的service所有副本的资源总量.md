```shell
统计指定命名空间下的service所有副本的资源总量: 内网
[root@iZqj0017jrl4zeu4rm66vbZ ~]# cat c.sh 
#!/bin/bash

DataFile="/app/$(date +%F)/resource_usage_$(date +%H-%M).txt"
mkdir -p /app/$(date +%F)
touch $DataFile


for j in app office task gateway ops
do
  echo "==============$j==============" &>> $DataFile

  case $j in 
  
  app)
    NS_LISTS="ecloud-app-uat"
    SERVICES="ecloud-app-sso ecloud-app-user ecloud-app-ajfl ecloud-app-ayps-fmss ecloud-app-ayps-fmxxpctfl ecloud-app-ayps-fs ecloud-app-ayps-pctgjjshcs ecloud-app-ayps-processor ecloud-app-ayps-syxx ecloud-app-ayps-wgsj ecloud-app-ayps-wgsjfl ecloud-app-ayps-wx ecloud-app-base ecloud-app-bbzz ecloud-app-bm ecloud-app-csbj ecloud-app-cxtj ecloud-app-cxtj-ghs ecloud-app-dzsl ecloud-app-dzsq-guojia ecloud-app-dzsq-pct ecloud-app-dzwjj ecloud-app-epct ecloud-app-file ecloud-app-file-browse ecloud-app-file-consumer ecloud-app-file-station ecloud-app-file-trans ecloud-app-flsx ecloud-app-fmcs ecloud-app-fmss ecloud-app-fswx ecloud-app-fw ecloud-app-gkgg ecloud-app-glhb ecloud-app-hysc ecloud-app-jarvis ecloud-app-jkfw ecloud-app-mk-zdza ecloud-app-nbbd ecloud-app-notice ecloud-app-notice-international ecloud-app-pctcs ecloud-app-pctgjlcgl ecloud-app-pph ecloud-app-print ecloud-app-public-user ecloud-app-scrwgl ecloud-app-sczlgl ecloud-app-sczqgl ecloud-app-sfgl ecloud-app-sqzlgl ecloud-app-syxxgl ecloud-app-syxxrule ecloud-app-syxxsc ecloud-app-user-sync-consumer ecloud-app-user-sync-producer ecloud-app-wgfl ecloud-app-wgqtfw ecloud-app-websocket ecloud-app-wgsj-sc ecloud-app-xzfy ecloud-app-yzlsc ecloud-app-zjgl ecloud-app-zjsl-guojia ecloud-app-zjsl-hague ecloud-app-zjsl-pct ecloud-app-zldjyp ecloud-app-zljffw ecloud-app-zlspjk ecloud-app-zlswfw ecloud-app-zlywbl"
  ;;
  
  office)
    NS_LISTS="ecloud-office-uat"
    SERVICES="ecloud-office-cbsqzjy ecloud-office-data-domestic ecloud-office-data-statistics ecloud-office-domestic ecloud-office-file ecloud-office-fw ecloud-office-international ecloud-office-log ecloud-office-log-extranet ecloud-office-message ecloud-office-message-processor ecloud-office-notice ecloud-office-rule ecloud-office-ruleengine ecloud-office-ruleengine-cepct ecloud-office-ruleengine-flsx ecloud-office-ruleengine-pct ecloud-office-ruleengine-pph ecloud-office-todo ecloud-office-user "
  ;;
  
  task)
    NS_LISTS="ecloud-task-uat"
    SERVICES="ecloud-task-flsx ecloud-task-domestic ecloud-task-sfgl "
  ;;
  
  gateway)
    NS_LISTS="ecloud-gateway-uat "
    SERVICES="ecloud-gateway ecloud-kong "
  ;;
  
  ops)
    NS_LISTS="ecloud-ops-uat"
    SERVICES="ecloud-ops-admin ecloud-ops-job ecloud-ops-konga ecloud-ops-tinyid ecloud-ops-turbine ecloud-ops-zipkin "
  ;;
  
  esac


  for i in ${SERVICES}
  do 
    ReplicaSet=$(kubectl get deployments -n $NS_LISTS $i -o yaml |grep -w ReplicaSet | awk -F '[ "]+' '{print $4}')
    kubectl top -n $NS_LISTS pod |grep -w $ReplicaSet  | awk 'BEGIN{C=0;M=0} {C+=$2~/m$/?$2/1000:$2;M+=$3~/Mi$/?$3/1024:$3}END{print "'$i':","cpu:"C"C","mem:"M"G"}' &>> $DataFile
  done
  
  echo "" &>> $DataFile

done 


kubectl top pods -n ecloud-office-uat |grep ecloud-office-flow | awk 'BEGIN{C=0;M=0} {C+=$2~/m$/?$2/1000:$2;M+=$3~/Mi$/?$3/1024:$3}END{print "'ecloud-office-flow':","cpu:"C"C","mem:"M"G"}' &>> $DataFile
kubectl top pod -n ecloud-ops-uat |grep ecloud-ops-zipkin-dependencies-job | awk 'BEGIN{C=0;M=0} {C+=$2~/m$/?$2/1000:$2;M+=$3~/Mi$/?$3/1024:$3}END{print "'ecloud-ops-zipkin-dependencies-job':","cpu:"C"C","mem:"M"G"}' &>> $DataFile

```



```shell
统计指定命名空间下的service所有副本的资源总量: 公众

[root@iZqj0013pchi586zk7h08aZ ~]# cat c.sh
#!/bin/bash

DataFile="/app/$(date +%F)/resource_usage_$(date +%H-%M).txt"
mkdir -p /app/$(date +%F)
touch $DataFile


for j in app gateway ops basic
do
  echo "==============$j==============" &>> $DataFile

  case $j in

  app)
    NS_LISTS="public-app-uat "
    SERVICES="public-app-sso public-app-user public-app-base public-app-comment public-app-file public-app-jkfw public-app-log public-app-m2m-dzsq-guojia public-app-m2m-hague public-app-m2m-pct-is public-app-m2m-wipoepct public-app-user-sync-consumer public-app-user-sync-producer public-app-ydd public-app-zljffw public-app-zlswfw public-app-zlywbl public-app-zxsq-guojia public-app-zxsq-pct public-office-message public-office-message-processor "
  ;;

  gateway)
    NS_LISTS="public-gateway-uat "
    SERVICES="public-gateway public-kong "
  ;;

  ops)
    NS_LISTS="public-ops-uat "
    SERVICES="public-ops-job public-ops-admin ecloud-ops-img-code public-ops-konga public-ops-oss public-ops-turbine public-ops-zipkin "
  ;;

  basic)
    NS_LISTS="g-basic-uat"
    SERVICES="g-application-server g-basic-core"
  ;;

  esac


  for i in ${SERVICES}
  do
    ReplicaSet=$(kubectl get deployments -n $NS_LISTS $i -o yaml |grep -w ReplicaSet | awk -F '[ "]+' '{print $4}')
    kubectl top -n $NS_LISTS pod |grep -w $ReplicaSet  | awk 'BEGIN{C=0;M=0} {C+=$2~/m$/?$2/1000:$2;M+=$3~/Mi$/?$3/1024:$3}END{print "'$i':","cpu:"C"C","mem:"M"G"}' &>> $DataFile
  done

  echo "" &>> $DataFile

done


kubectl top pods -n public-ops-uat |grep public-ops-nacos | awk 'BEGIN{C=0;M=0} {C+=$2~/m$/?$2/1000:$2;M+=$3~/Mi$/?$3/1024:$3}END{print "'ecloud-office-flow':","cpu:"C"C","mem:"M"G"}' &>> $DataFile
kubectl top pod -n public-ops-uat |grep public-ops-zipkin-dependencies-job | awk 'BEGIN{C=0;M=0} {C+=$2~/m$/?$2/1000:$2;M+=$3~/Mi$/?$3/1024:$3}END{print "'ecloud-ops-zipkin-dependencies-job':","cpu:"C"C","mem:"M"G"}' &>> $DataFile

```

```shell
==============app==============
public-app-sso: cpu:0.512C mem:19.9629G
public-app-user: cpu:0.77C mem:18.3936G
public-app-base: cpu:0.746C mem:18.8203G
public-app-comment: cpu:0.158C mem:16.9619G
public-app-file: cpu:0.402C mem:20.0537G
public-app-jkfw: cpu:0.709C mem:18.0957G
public-app-log: cpu:0.313C mem:15.4053G
public-app-m2m-dzsq-guojia: cpu:0.601C mem:12.4619G
public-app-m2m-hague: cpu:0.039C mem:11.0352G
public-app-m2m-pct-is: cpu:0.12C mem:11.916G
public-app-m2m-wipoepct: cpu:0.006C mem:0.638672G
public-app-user-sync-consumer: cpu:0.111C mem:13.0996G
public-app-user-sync-producer: cpu:0.287C mem:11.1465G
public-app-ydd: cpu:0.656C mem:17.2266G
public-app-zljffw: cpu:0.834C mem:19.7891G
public-app-zlswfw: cpu:0.745C mem:19.8125G
public-app-zlywbl: cpu:0.449C mem:16.5537G
public-app-zxsq-guojia: cpu:0.033C mem:1.27734G
public-app-zxsq-pct: cpu:0.829C mem:21.0625G
public-office-message: cpu:0.23C mem:19.8174G
public-office-message-processor: cpu:0.231C mem:16.0547G

==============gateway==============
public-gateway: cpu:0.211C mem:20.1123G
public-kong: cpu:0.067C mem:3.52637G

==============ops==============
public-ops-job: cpu:0C mem:0G
public-ops-admin: cpu:0.04C mem:3.95117G
ecloud-ops-img-code: cpu:0.018C mem:25.7793G
public-ops-konga: cpu:0.283C mem:0.131836G
public-ops-oss: cpu:0C mem:0.0273438G
public-ops-turbine: cpu:0.013C mem:6.74805G
public-ops-zipkin: cpu:0.01C mem:2.17676G

==============basic==============
g-application-server: cpu:0.006C mem:1.49805G
g-basic-core: cpu:0.005C mem:0.258789G

ecloud-office-flow: cpu:0.023C mem:5.79492G
ecloud-ops-zipkin-dependencies-job: cpu:0.108C mem:1.28809G

```

