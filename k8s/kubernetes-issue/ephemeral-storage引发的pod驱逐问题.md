**目录**

**(1).问题现象**

**(2).问题查证过程**

**(3).原因总结**

**(4).解决方式**

**正文**

**大前提：单节点2核16G搭建微服务完整的**[**容器**](https://cloud.tencent.com/product/tke?from=10680)**化环境，需要容器化很多微服务组件。**

**(1).问题现象**

describe不能启动的pod,发现两条关键日志：

A.0/1 nodes are available: 1 node(s) had taints that the pod didn't tolerate.

说明当前所在work-node资源不容许此pod部署，发生了pod驱逐。

B.**The node was low on resource: ephemeral-storage.**

说明了发生pod驱逐的原因。

**(2).问题查证过程**

**1.ephemeral-storage(短暂存储)的概念和作用**

ephemeral-storage是为了管理和调度Kubernetes中运行的应用的短暂存储。

在每个Kubernetes的节点上，kubelet的根目录(默认是/var/lib/kubelet)和日志目录(/var/log)保存在节点的主分区上，这个分区同时也会被Pod的EmptyDir类型的volume、容器日志、镜像的层、容器的可写层所占用。ephemeral-storage便是对这块主分区进行管理，通过应用定义的需求(requests)和约束(limits)来调度和管理节点上的应用对主分区的消耗。

我们使用df -h可以看到/var/lib下有很多容器相关的目录，这些都是ephemeral-storage：

**2.image镜像重新下载**

查证时无意中发现pod在启动时要重新下载相关镜像，说明由于磁盘容量不够时，kubernetes会清理镜像文件去腾空磁盘，省出资源去优先启动pod。

符合上述1中所述的“镜像层占用ephemeral-storage”。

因为笔者机器打算做一个单节点的kubernetes集群，去部署微服务的allinone环境，image会很多，占用空间会很大。原有的20G根本不够用。

**3.本地pv存储占用磁盘**

如2所述，kubernetes中的allinone的各个组件的持久化存储都是用的本地盘(系统盘)，都开的是1G，那么原有的20G更不够了。

**4.有些容器化组件没有使用持久存储**

造成应用的日志全部打印到了容器里，也就是/var/log目录下，耗用了ephemeral-storage。

**(3).原因总结**

根本原因：Kubernetes使用的系统盘容量不足；触发项如下：

1.image镜像太多，耗用太多ephemeral-storage；

2.本地pv存储全部挂载了系统盘目录下；

3.有些容器没有使用持久化存储，日志等全部打印到了ephemeral-storage。

**(4).解决方式**

原则：让ephemeral-storage只存储kubernetes体系下的日志，系统盘容量要充足。

1.image镜像层的存储指定非系统盘；

2.本地pv存储挂载非系统盘；**(生产环境禁止使用本地磁盘，必须使用**[**云存储**](https://cloud.tencent.com/product/cos?from=10680)**)**

3.容器必须都使用持久化存储，禁止把日志都打到容器里。