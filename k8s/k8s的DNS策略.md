# ***\*从一次k8s容器内域名解析失败学习k8s的DNS策略\****

k8s的DNS相关内容，详见[官方文档地址](https://links.jianshu.com/go?to=https%3A%2F%2Fkubernetes.io%2Fzh%2Fdocs%2Fconcepts%2Fservices-networking%2Fdns-pod-service%2F)

### ***\*一、k8s默认的DNS策略\****

k8s提供了5种DNS策略，如下：

· Default: Pod 从运行所在的节点继承名称解析配置。

· ClusterFirst: 与配置的集群域后缀不匹配的任何 DNS 查询（例如 “[www.kubernetes.io](https://links.jianshu.com/go?to=http%3A%2F%2Fwww.kubernetes.io)”） 都将转发到从节点继承的上游名称服务器。集群管理员可能配置了额外的存根域和上游 DNS 服务器。

· ClusterFirstWithHostNet：对于以 hostNetwork 方式运行的 Pod，应显式设置其 DNS 策略 ClusterFirstWithHostNet。

· None: 此设置允许 Pod 忽略 Kubernetes 环境中的 DNS 设置。Pod 会使用其 dnsConfig 字段 所提供的 DNS 设置。

k8s默认使用的DNS策略是ClusterFirst，这点需要注意，也就是说域名解析会优先使用集群的DNS（kube-DNS）进行查询，如果k8s的DNS解析失败，会转发到宿主机的DNS进行解析。

### ***\* \*******\*二、k8s容器的\****resolv.conf 

k8s上运行的容器，其域名解析和一般的Linux一样，都是根据 /etc/resolv.conf 文件进行解析，下面看一个开发环境某一个pod的resolv.conf内容：

```shell
nameserver 10.96.0.10
search jplat.svc.cluster.local svc.cluster.local cluster.local localhost
options ndots:5
```

上面内容中nameserver即为k8s集群中kube-dns的Service的CLUSTER-IP，该集群中容器的nameserver均为kube-dns的ip。

search和options ndots我们一起来讲。首先我们需要了解一个概念FQDN(Fully qualified domain name)即完整域名。一般来说如果一个域名以.结束，就表示一个完整域名。比如www.abc.xyz.就是一个FQDN，而www.abc.xyz则不是FQDN。了解了这个概念之后我们就来看search和options ndots。

如果我们的pod使用的是默认的DNS策略，即ClusterFirst，那么如果一个域名是FQDN，那么这个域名会被转发给DNS服务器进行解析。如果域名不是FQDN，那么这个域名会到search搜索解析，还是通过一个例子说明，比如访问abc.xyz这个域名，因为它并不是一个FQDN，所以它会和search域中的值进行组合而变成一个FQDN，以上文的resolv.conf为例，这域名会这样组合：

```shell
abx.xyz.jplat.svc.cluster.local.
abc.xyz.svc.cluster.local.
abc.xyz.cluster.local.
...
```

然后这些域名先被kube-DNS解析，如果没有解析成功再由宿主机的DNS服务器进行解析。

 

而ndots是用来表示一个域名中.的个数在不小于该值的情况下会被认为是一个FQDN。简单说这个属性用来判断一个不是以.结束的域名在什么条件下会被认定为是一个FQDN。还是通过我们另一个pod的resolv.conf为例，如下：

```shell
nameserver 10.96.0.10
search oms-dev.svc.cluster.local svc.cluster.local cluster.local localhost
options ndots:2 edns0
```

在这个resolv.conf中ndots为2，也就是说如果一个域名中.的数量大于等于2，即使域名不是以.结尾，也会被认定为是一个FQDN。比如：域名是abc.xyz.xxx这个域名就是FQDN，而abc.xyz则不是FQDN。

之所以会有search域主要还是为了方便k8s内部服务之间的访问。比如：k8s在同一个namespace下是可以直接通过服务名称进行访问的，其原理就是会在search域查找，比如上面的resolv.conf中jplat、oms-dev着两个其实都是这两个pod所在的namespace的名称。所以通过服务名称访问的时候，会和search域进行组合，这样最终域名会组合成servicename.namespace.svc.cluster.local。而如果是跨namespace访问，则可以通过servicename.namespace这样的形式，在通过和search域组合，依然可以得到servicename.namespace.svc.cluster.local。

 

### ***\*三、解决问题\****

在了解k8s的DNS相关知识之后，回到我们项目组在开发中遇到一个问题：一个应用的pod内调用外部接口失败，报错的原因就是unknown host，最开始我以为是外部应用没在一个网段的问题，但是直接通过服务器却可以访问的。当时pod使用的是默认的DNS策略，即ClusterFirst，ndots也是默认值，即5。问题出现了。我请求的域名是www.abc.com/api，域名只有2个点，所以会和search域的域名进行组合，结果当然无法解析。考虑到我们的pod访问都是在同一namespace下，即使跨namespace，我们也是通过servicename.namespace进行访问的，所以最终选择将ndots改为2。修改应用的Deployment yaml文件，修改如下：

```shell
      dnsConfig:
        options:
          - name: ndots
            value: "2"
```

修改完成之后重新部署项目，测试解决。