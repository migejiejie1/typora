# 读完这篇文章，理解不了statefuset类型你过来打我



## 简介

statefulset官方定义是用于有状态服务，其实不是太准确，我的理解应该是用于`有状态服务的集群`，例如单节点的mysql，根本不需要statefulset,deployment就已经足够了。

为什么是集群呢？我们带着这个疑问往下看。。

首先了解一下集群，集群通常是有多个节点，节点之间有多个角色，有的节点是主节点，有的是从节点，有的是备节点。 主节点有的是负责写数据作用，从节点有的是负责读数据作用，有的主节点和从节点都可以读写数据，到这里可以看到每个节点都各司其职。

**就拿一个最简单的mysql读写分离为例，我们在程序中大致需要配置如下，很明显，host为集群中某个具体的节点的ip，而不是集群中随意的一个节点的ip，因为节点的角色已经固定，而不是像我们的业务代码一样，横向扩展多个节点，然后上层部署一个负载均衡，流量到达负载均衡时随机访问集群中的任意一个节点即可。**



```dart
'mysql' => [
    'read' => [
        ['host' => '192.168.2.100', 'password' => '12345'],
        ['host' => '192.168.2.101', 'password' => '12345']
    ],
    'write' => [
        ['host' => '192.168.2.102', 'password' => '12345']
    ]
]
```

## statefulset与deployment的区别

在上面的那段描述中，其实已经很清楚了，deployment适合于类似`业务代码`这种无角色之分，无状态，可横向扩展的应用。**访问deployment的service时，service会通过kube-proxy`随机`访问一个pod。**

而statefulset更适合`有状态服务的集群`,类似mysql集群，redis集群，mogodb集群，es集群等。。在业务代码中访问这个有状态服务的集群时，通过需要`指定具体的pod`来执行具体的操作，例如读操作，写操作，而不能像deployment那样随机访问一个pod，那么statefulset怎么是怎能来实现这个的呢？答案就是headless service。

## headlless service

定义一个clusterIP: None的service即为一个headlless service，如下。



```python
kind: Service
apiVersion: v1
metadata:
  name: es-headless-service
spec:
  clusterIP: None
  ports:
    - protocol: TCP
      name: web
      port: 9300
      targetPort: 9300
  selector:
    name: es-cluster
```

然后定义一个statefulset,并指定headless service,大致如下



```kotlin
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: es-cluster
spec:
  selector:
    matchLabels:
      name: es-cluster # has to match .spec.template.metadata.labels
  serviceName: es-headless-service
  replicas: 3 # by default is 1
  template:
    metadata:
      labels:
        name: es-cluster # has to match .spec.selector.matchLabels
    spec:
      containers:
        - name: elasticsearch
          image: harbor.maigengduo.com/elk/elasticsearch:6.8.9
         volumeMounts:
            - name: es-data
              mountPath: /usr/share/elasticsearch/data
  volumeClaimTemplates:
    - metadata:
        name: es-data
      spec:
        accessModes: [ "ReadWriteMany"]
        storageClassName: "es-storage"
        resources:
          requests:
            storage: 1Gi
```

上面介绍怎么定义一个headless service,接下来介绍下怎么可以稳定的访问到具体的pod呢？

首先要知道访问具体的pod有俩种方式，访问podIp,或者dns解析podName,由于pod每次部署ip都会改变，所以想要访问具体的pod肯定是以第二种方式，使用这种方式必须保证pod具有稳定的标示。

### pod标示

StatefulSet Pod 具有唯一的标识，该标识包括`顺序标识`、`稳定的网络标识`和`稳定的存储`。 **该标识和 Pod 是绑定的，不管它被调度在哪个节点上**。

**稳定的意思就是pod被重新调度之后所有的资源必须和调度之前的是相同的。**

#### 有序索引

对于具有 N 个副本的 StatefulSet，StatefulSet 中的每个 Pod 将被分配一个整数序号， 从 0 到 N-1，该序号在 StatefulSet 上是唯一的。

#### 稳定的网络 ID

StatefulSet 中的每个 Pod 根据 StatefulSet 的名称和 Pod 的序号派生出它的主机名。 组合主机名的格式为`$(StatefulSet 名称)-$(序号)`。上例将会创建三个名称分别为 es-cluster-0、es-cluster-1、es-cluster-2 的 Pod。

稳定的意思就是，比如podName为`es-cluster-0`为的pod被重新调度后，podName必须仍为`es-cluster-0`，而不是`es-cluster-1`或者`es-cluster-2`。

还有需要知道的一点是statefulest中的pod的hostName和podName是相同的。

#### 稳定的存储

statefulset中的每个pod的存储资源是`不共享`的，**这个特性正好和集群中节点分片存储是一个概念。**在deploymen中的pod间的存储资源是共享的。

稳定的存储就是**pod重新调度后还是能访问到相同的持久化数据，基于pvc实现**，我们称之为`稳定的不共享持久化存储`

为什么能达到稳定的存储呢？
statefulset的pod存储使用pvc实现的，pv的共享目录下会存在和pod数相同的目录，且目录名包含podName,只要保证了保证podName有稳定的标示，也就意味着会有稳定的存储了。pv使用的是nfs,共享目录为/es-data,可以发现目录名为`namespace-shareDir-podName-$(序号)-pvc-随机数`



```kotlin
[root@002 es-data]# ll
total 12
drwxrwxrwx 3 root root 4096 Dec 15 13:51 laravel-es-data-es-cluster-0-pvc-4461cb91-4ba9-4300-bba8-cce2079a03ba
drwxrwxrwx 3 root root 4096 Dec 15 13:51 laravel-es-data-es-cluster-1-pvc-fa711157-5bae-4f6b-aa94-9e1a9132316a
drwxrwxrwx 3 root root 4096 Dec 15 13:51 laravel-es-data-es-cluster-2-pvc-9569e705-e047-47f9-9014-ca09a2220df9
```

### 访问具体的pod

上面的介绍我们知道了，**headlesss service可以保证pod具有稳定的网络标示**。但是我们该怎么访问具体的某个pod呢？

我们知道deployment中的service具有serviceIp，访问service时，通过kube-proxy随机分发到endPoint列表中一个podIp。

但是headless service是没有ip的,我们该怎么访问pod呢？



```dart
root@master ~]# kubectl get svc -n laravel
NAME                   TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
es-headless-service   ClusterIP   None             <none>        9300/TCP         10d
```

答案就是：`podName.headlessServiceName`
比如我们想访问podName为es-cluster-0的pod,访问方式就是es-cluster-0.es-headless-service,想访问podName为es-cluster-1的pod,访问方式就是es-cluster-1.es-headless-service。

比如es-cluster-0为主节点用于写数据，es-cluster-1，es-cluster-2是从节点用于读数据，此时我们在业务代码中就可以很好的访问具体的某个节点了。

为了验证这些，我们首先查看statefulset pod,注意pod ip



```swift
[root@master ~]# kubectl get pod -n laravel -o wide
NAME                                READY   STATUS    RESTARTS   AGE     IP               NODE      NOMINATED NODE   READINESS GATES
es-cluster-0                        1/1     Running   0          6d18h   172.22.235.135   worker1   <none>           <none>
es-cluster-1                        1/1     Running   0          6d18h   172.22.189.119   worker2   <none>           <none>
es-cluster-2                        1/1     Running   0          6d18h   172.22.235.138   worker1   <none>           <none>
```

然后进去es-cluster-0内,ping es-cluster-1.es-headless-service,发现返回的ip正好是es-cluster-1的ip172.22.189.119 。



```csharp
[root@es-cluster-0 elasticsearch]# ping es-cluster-1.es-headless-service
PING es-cluster-1.es-cluster-discovery.laravel.svc.cluster.local (172.22.189.119) 56(84) bytes of data.
64 bytes from 172-22-189-119.es-cluster-api.laravel.svc.cluster.local (172.22.189.119): icmp_seq=1 ttl=62 time=0.240 ms
64 bytes from 172-22-189-119.es-cluster-api.laravel.svc.cluster.local (172.22.189.119): icmp_seq=2 ttl=62 time=0.420 ms
64 bytes from 172-22-189-119.es-cluster-api.laravel.svc.cluster.local (172.22.189.119): icmp_seq=3 ttl=62 time=0.396 ms
```

> 直接访问headlessService相当于访问StatefulSetName-0.headlessService,这点需要注意。

发现ping es-headless-service,返回的ip是es-cluster-0的ip172.22.235.135。



```csharp
root@es-cluster-0 elasticsearch]# ping es-headless-service
PING es-cluster-discovery.laravel.svc.cluster.local (172.22.235.135) 56(84) bytes of data.
64 bytes from es-cluster-0.es-cluster-discovery.laravel.svc.cluster.local (172.22.235.135): icmp_seq=1 ttl=64 time=0.028 ms
64 bytes from es-cluster-0.es-cluster-discovery.laravel.svc.cluster.local (172.22.235.135): icmp_seq=2 ttl=64 time=0.054 ms
64 bytes from es-cluster-0.es-cluster-discovery.laravel.svc.cluster.local (172.22.235.135): icmp_seq=3 ttl=64 time=0.050 ms
```

## 角色分离

节点之间有角色之分，主节点，从节点，备节点，在传统的业务中修改对应角色节点的配置文件即可，但是在statefulset中的pod使用的都是相同的镜像，也就是镜像的内的配置文件都是相同的，那么我们怎么来区分节点角色呢？

就拿mysql主从配置来说吧，我们可以建一个configmap, 用于存储不同角色的配置文件。



```bash
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysql
  namespace: mysql
  labels:
    app: mysql
data:
  master.cnf: |
    # Master配置
    [mysqld]
    log-bin=mysqllog
    skip-name-resolve
  slave.cnf: |
    # Slave配置
    [mysqld]
    super-read-only
    skip-name-resolve
    log-bin=mysql-bin
    replicate-ignore-db=mysql
```

**Init 容器是一种特殊容器，在pod内的应用容器启动之前运行。Init 容器可以包括一些应用镜像中`不存在的实用工具和安装脚本`**。由于init容器的这个特性，我们可以在statefulset中的[init容器](https://links.jianshu.com/go?to=https%3A%2F%2Fkubernetes.io%2Fzh%2Fdocs%2Fconcepts%2Fworkloads%2Fpods%2Finit-containers%2F)中灵活的分配不同角色pod的配置文件,大概如下，具体可以参考这篇[文章](https://links.jianshu.com/go?to=https%3A%2F%2Flihaoquan.me%2F2020%2F3%2F6%2Fmysql-master-slave-statefulset.html),主要是学习这种思想。



```bash
    initContainers:
      - name: init-mysql
        image: mysql:5.7
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: password
        command:
        - bash
        - "-c"
        - |
          set -ex
          # 从 Pod 的序号，生成 server-id
          [[ $(hostname) =~ -([0-9]+)$ ]] || exit 1
          ordinal=${BASH_REMATCH[1]}
          echo [mysqld] > /mnt/conf.d/server-id.cnf
          # 由于 server-id 不能为 0，因此给 ID 加 100 来避开它
          echo server-id=$((100 + $ordinal)) >> /mnt/conf.d/server-id.cnf
          # 如果 Pod 的序号为 0，说明它是 Master 节点，从 ConfigMap 里把 Master 的配置文件拷贝到 /mnt/conf.d 目录下
          # 否则，拷贝 ConfigMap 里的 Slave 的配置文件
          if [[ ${ordinal} -eq 0 ]]; then
            cp /mnt/config-map/master.cnf /mnt/conf.d
          else
            cp /mnt/config-map/slave.cnf /mnt/conf.d
          fi
        volumeMounts:
        - name: conf
          mountPath: /mnt/conf.d
        - name: config-map
          mountPath: /mnt/config-map
      ......
      ......
      ......
      volumes:
      - name: conf
        emptyDir: {}
      - name: config-map
        configMap:
          name: mysql
```

## statefulset 案例

总结一下statefulset的特点

- 稳定的不共享持久化存储：即每个pod的存储资源是不共享的，也就是每个pod为`分片存储`。
- 节点之间有角色之分，业务代码需要访问具体的某个pod。
- 稳定的、唯一的网络标识符。
- 有序的、优雅的部署和缩放。
- 有序的、自动的滚动更新。

只要满足上面一种情况就可以stafulset了。

### mysql

针对mysql的主从集群，进行分析。

首先主从结构，具有主节点和从节点，复杂点的有备节点角色之分，主节点用于写数据，从节点用于读数据，在业务代码中需要访问到具体的pod，因此肯定需要的是statefulset,而不是deployment。

其次有人肯定会问，statefulset中的pod存储是分片存储的，而mysql主从并不是分片存储的，适用于statefulset类型吗？
mysql主节点是负责写数据的，然后会异步同步到从节点，而从节点只会读数据，并不会写数据，所以pod分片存储这个特性并不影响使用statefulset。

### es

es集群节点有master和data角色之分,master节点负责创建索引，删除索引和追踪集群中节点的状态等，data节点负责数据的存储和相关的增删改查操作。

**es中的所有节点都是协调节点，就是每个节点都可以转发流量。在一个读或者写请求被发送到某个节点后，该节点即为前面说过的协调节点，协调节点会根据路由公式计算出需要读或者写到哪个分片上，再将请求转发到该分片的主分片节点上。**也就是说在业务代码中并不需要像mysql主从那样作用到具体的某个pod上，所以我们可以创建一个clusterIP的service用于业务访问，监听9200端口。

而es集群间的pod是需要分片存储的，正因为这个es集群必须使用statefulset来部署。

## 参考

[Kubernetes部署本地有状态mysql主从服务](https://links.jianshu.com/go?to=https%3A%2F%2Flihaoquan.me%2F2020%2F3%2F6%2Fmysql-master-slave-statefulset.html)