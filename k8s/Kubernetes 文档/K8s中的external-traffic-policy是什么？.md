# K8s中的external-traffic-policy是什么？

【摘要】 external-traffic-policy，顾名思义“外部流量策略”，那这个配置有什么作用呢？以及external是指什么东西的外部呢，集群、节点、Pod？今天我们就来学习一下这个概念吧。

# 1   什么是external-traffic-policy

在k8s的Service对象（申明一条访问通道）中，有一个“externalTrafficPolicy”字段可以设置。有2个值可以设置：Cluster或者Local。

1）Cluster表示：流量可以转发到其他节点上的Pod。

2）Local表示：流量只发给本机的Pod。

图示一下：

![image-20230131111124877](../../Image/image-20230131111124877.png)

# 2   这2种模式有什么区别

存在这2种模式的原因就是，当前节点的Kube-proxy在转发报文的时候，会不会保留原始访问者的IP。

## 2.1   选择（1）Cluster

注：这个是默认模式，Kube-proxy不管容器实例在哪，公平转发。

Kube-proxy转发时会替换掉报文的源IP。即：容器收的报文，源IP地址，已经被替换为上一个转发节点的了。

![image-20230131111140377](../../Image/image-20230131111140377.png)

原因是Kube-proxy在做转发的时候，会做一次SNAT (source network address translation)，所以源IP变成了节点1的IP地址。

ps：snat确保回去的报文可以原路返回，不然回去的路径不一样，客户会认为非法报文的。（我发给张三的，怎么李四给我回应？丢弃！）

这种模式好处是负载均衡会比较好，因为无论容器实例怎么分布在多个节点上，它都会转发过去。当然，由于多了一次转发，性能会损失一丢丢。

## 2.2   选择（2）Local

这种情况下，只转发给本机的容器，绝不跨节点转发。

Kube-proxy转发时会保留源IP。即：容器收到的报文，看到源IP地址还是用户的。

![image-20230131111152845](../../Image/image-20230131111152845.png)

缺点是负载均衡可能不是很好，因为一旦容器实例分布在多个节点上，它只转发给本机，不跨节点转发流量。当然，少了一次转发，性能会相对好一丢丢。

注：这种模式下的Service类型只能为外部流量，即：LoadBalancer 或者 NodePort 两种，否则会报错。

同时，由于本机不会跨节点转发报文，所以要想所有节点上的容器有负载均衡，就需要上一级的Loadbalancer来做了。

![image-20230131111323186](../../Image/image-20230131111323186.png)

不过流量还是会不太均衡，如上图，Loadbalancer看到的是2个后端（把节点的IP），每个Node上面几个Pod对Loadbalancer来说是不知道的。

想要解决负载不均衡的问题：可以给Pod容器设置反亲和，让这些容器平均的分布在各个节点上（不要聚在一起）。

```javascript
affinity:
  podAntiAffinity:
    preferredDuringSchedulingIgnoredDuringExecution:
    - weight: 100
      podAffinityTerm:
        labelSelector:
          matchExpressions:
           - key: k8s-app
             operator: In
             values:
             - my-app
        topologyKey: kubernetes.io/hostname
```

像下面这样，负载均衡情况就会好很多~

![image-20230131111335070](../../Image/image-20230131111335070.png)

# 3   两种模式该怎么选

要想性能（时延）好，当然应该选 Local 模式喽，毕竟流量转发少一次SNAT嘛。

不过注意，选了这个就得考虑好怎么处理好负载均衡问题（ps：通常我们使用Pod间反亲和来达成）。

  如果你是从外部LB接收流量的，那么使用：Local模式 + Pod反亲和，一般是足够的。