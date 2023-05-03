# externalTrafficPolicy=Local 理解

## 概述

对于`NodePort` 类型的 `service` 而言，有一个参数是 `externalTrafficPolicy=Local`

即 `service.spec.externalTrafficPolicy=Local`

刚好最近机缘巧合之下对这个参数了解了一下，于是把我的个人理解写在这里，分享给大家

## 前言

对于`Service`, 如果指定类型为 `NodePort`, 那么这个端口会在集群的所有 `Node` 上打开，即使这个`Node` 上面没有这个pod
(很好理解，和守护进程集不一样，对于`Deployment` 来说，很少会在每个节点上都启动pod，所以必定有一些节点上没有这个pod)

引出一个问题，当某个节点上没有`pod`的时候，又去访问ta的这个`NodePort`,能访问到吗？

## 流程1

答案是可以的，[官方文档](https://links.jianshu.com/go?to=https%3A%2F%2Fkubernetes.io%2Fdocs%2Ftutorials%2Fservices%2Fsource-ip%2F%23source-ip-for-services-with-type-nodeport)

流程大概是这样的



```ruby
          client
             \ ^
              \ \
               v \
   node 1 <--- node 2
    | ^   SNAT
    | |   --->
    v |
 endpoint
```

- Client sends packet to node2:nodePort
  客户端发送 tcp 包 到 node2:nodePort
- node2 replaces the source IP address (SNAT) in the packet with its own IP address
  node2 把客户端源ip地址替换为node2 的ip地址
- node2 replaces the destination IP on the packet with the pod IP
  node2 把请求的目的地ip替换为 pod ip
- packet is routed to node 1, and then to the endpoint
  tcp包被路由到 node1， 接着达到 endpoint(如service)
- the pod’s reply is routed back to node2
  pod的响应被路由到 node2
- the pod’s reply is sent back to the client
  node2把pod的响应发送给客户端

可以发现，在这个过程中，客户端的源IP地址丢失了（看第二步）

## 流程2

为了解决这个问题， `k8s` 提供了一个功能，通过设置 `externalTrafficPolicy=Local` 可以保留源IP地址

设置完这个参数之后，流程如下



```ruby
        client
       ^ /   \
      / /     \
     / v       X
   node 1     node 2
    ^ |
    | |
    | v
 endpoint
```

- client sends packet to node2:nodePort, which doesn’t have any endpoints
  客户端发送tcp包到 node2:nodePort, 但是 node2 并没有 这个pod
- packet is dropped
  tcp包被丢弃
- client sends packet to node1:nodePort, which does have endpoints
  客户端发送数据包到 node1:nodePort, node1有pod
- node1 routes packet to endpoint with the correct source IP
  node1 把包路由到对应的pod，那么pod 就可以拿到正确的客户端源IP地址