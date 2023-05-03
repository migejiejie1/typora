```bash
#!/bin/bash
# 统计wgqtfwPod下的 /wgzstxqy/ 目录的大小，当该目录大于指定大小时，重启该服务
# 通过移除pod的label标签，来实现重启pods,最优雅的方式

namespace="ecloud-app"
podname="ecloud-app-wgqtfw"

wgqtfwPod=$(kubectl get pods -n $namespace |grep $podname |awk '{print $1}')

for i in $wgqtfwPod
do
  size=$(kubectl exec -it -n $namespace $i -- du -s /wgzstxqy/ |awk '{print $1}')
  echo $size
  
  if [[ $size -gt 10000000 ]];then
    echo 大于10G
	
    kubectl label pods -n $namespace $i workload.user.cattle.io/workloadselector-
    sleep 60
    kubectl delete pods -n $namespace $i 

  else
    echo 小于10G
  fi
  
done
```

```bash
pod重启：方式1：使用replace命令替换
可以使用replace结合force选项进行重启，前提是有之前启动时所使用的yaml文件

# kubectl replace --force -f busybox-pod-test.yaml 
pod "test-pod" deleted
pod/test-pod replaced

从结果中可以看出，此种方式实际上替换（replace）的过程是先进行删除然后再次创建的过程
```

```bash
pod重启：方式2：无yaml文件的replace方法
执行命令：kubectl get pod pod名称 -n 命名空间名称 -o yaml | kubectl replace --force -f -

没有yaml文件时可以使用-o yaml生成，然后再进行replace

# kubectl get pod test-pod -n default -o yaml | kubectl replace --force -f -
pod "test-pod" deleted
pod/test-pod replaced
```

```bash
pod重启：方式3：重新创建
相当于上述replace命令的手工执行，可以根据pod生成yaml文件进行创建，先生成创建的yaml文件

# kubectl get pod test-pod -n default -o yaml >ttt.yml

然后删除pod
# kubectl delete pod test-pod
pod "test-pod" deleted

然后重新创建pod
# kubectl create -f ttt.yml 
pod/test-pod created
```

```bash
pod重启：方式4：设定restartPolicy
前提是使用了Deployment或者直接是restartPolicy的设定不是Never，比如是Always，示例如下所示：

  restartPolicy: Always

实际使用的时候可以看出，由于此restartPolicy的作用，pod变为completed的时候会立即被重启
```

```bash
pod重启：方式5：直接删除Pod
前提：使用Deployment等方式的时候，相当于在pod之上又封了一层，所以此时直接删除pod，会有Deployment根据策略进行管控，一般直接删除即可，也可以调整replica来实现类似的效果。

# kubectl delete pod {podname} -n {namespace}
```

```shell
pod重启：方式6：使用scale命令
没有 yaml 文件，但是使用的是 Deployment 对象。可以使用以下方式重启

由于 Deployment 对象并不是直接操控的 Pod 对象，而是操控的 ReplicaSet 对象，而 ReplicaSet 对象就是由副本的数目的定义和Pod 模板组成的。所以这条命令分别是将ReplicaSet 的数量 scale 到 0，然后又 scale 到 1，那么 Pod 也就重启了。

#kubectl scale deployment esb-admin --replicas=0 -n {namespace}
#kubectl scale deployment esb-admin --replicas=1 -n {namespace}
```

```bash
pod重启：方式7：使用rollout restart 命令
Kubernetes 1.15开始才有
1.15版本之后可通过kubectl rollout restart deployment -n 命令来实现滚动重启POD
该命令会先创建待用POD，待新POD运行成功后，再关闭原有POD。因此需要保证node节点数量大于POD数量，否则新POD无法正常启动。

# kubectl rollout restart deploy {your_deployment_name}

当POD数量与node数量相同时，可使用先减小deployment规模的方法，先减小规模，再执行重启，待重启成功后再恢复deployment规模：
# kubectl scale deployment pluto --replicas=2 -n test
# kubectl rollout restart deployment pluto -n test
# kubectl scale deployment pluto --replicas=3 -n test
```

