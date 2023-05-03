**kubernetes的工作原理**

相信使用二进制部署过 k8s 集群的同学们都知道，二进制部署集群太困难了，有点基础的人部署起来还有成功的希望，要不然只能跟着别人的教程一步一步的去部署，部署的时候完全不知道这样操作的意义是啥？出问题了无从下手解决。对于初学者来说真的是浪费生命，那有没有什么简单的方式来部署集群呢？这个问题在前几年可能没有很好的答案，但是在现在，答案简直太多了，比如 kubeadm，rke 等方式，我们今天就来介绍下 kubeadm 部署集群的工作原理。

这里默认你有点 k8s 基础，知道他们都是有哪些组件构成的。

在集群部署的时候，他的每一个组件都是一个需要被执行的，单独的二进制文件，在现在容器化那么发达的时期，我们肯定来用 docker 来简化部署。但是容器化部署的时候会有一个很大的问题，***\*如何容器化 kubelet\****。

我们知道 kubelet 是 kubernetes 项目用来操作 Docker 等容器运行时的核心组件，在每个 节点上都存在，可以除了跟容器运行时打交道外，kubelet 在配置容器网络、管理容器数据卷时，他都需要直接操作宿主机。

如果现在 kubelet 本身就运行在一个容器里，那么直接操作宿主机就会变得很麻烦。对于网络配置来说还好，kubelet 容器可以通过不开启 Network Namespace（--net host）的方式，直接共享宿主机的网络栈。可是，要让 kubelet 隔着容器的 Mount Namespace 和文件系统，操作宿主机的文件系统，就有点儿困难了。

比如，如果用户想要使用 NFS 做容器的持久化数据卷，那么 kubelet 就需要在容器进行绑定挂载前，在宿主机的指定目录上，先挂载 NFS 的远程目录。

可是，这时候问题来了。由于现在 kubelet 是运行在容器里的，这就意味着它要做的这个“mount -F nfs”命令，被隔离在了一个单独的 Mount Namespace 中。即，kubelet 做的挂载操作，不能被“传播”到宿主机上。

对于这个问题，有人说，可以使用 setns() 系统调用，在宿主机的 Mount Namespace 中执行这些挂载操作；也有人说，应该让 Docker 支持一个–mnt=host 的参数。但是，到目前为止，在容器里运行 kubelet，依然没有很好的解决办法，我也不推荐你用容器去部署 Kubernetes 项目。

因此为了解决上面这个问题，kubeadm 选择了一种方案：

把 kubelet 直接部署在宿主机上，然后使用容器部署其他的 kubernetes组件

所以，我们使用 kubeadm 安装集群的第一步 就是在所有的机器上手动安装 kubeadm、kubelet 和 kubectl 这三个二进制文件。

Centos / Redhat 安装：

```shell
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF

yum -y install kubectl 
yum -y install kubelet
yum -y install kubeadm
systemctl enable kubelet  #必须要设置
```

**注意**： 如果你这个时候启动 kubelet 肯定会发现一个报错，提示缺少文件，这个时候不用管它，我们一会使用 kubeadm init 之后初始化集群就正常了

然后我们需要创建 kubeadm 的配置文件，指定我们集群信息。举个例子

```yaml
cat > kubeadm-config.yaml <<-'EOF'

apiVersion: kubeadm.k8s.io/v1alpha2
kind: MasterConfiguration
kubernetesVersion: v1.16.0
api:
  advertiseAddress: 192.168.0.102
  bindPort: 6443
  ...
etcd:
  local:
    dataDir: /var/lib/etcd
    image: ""
imageRepository: k8s.gcr.io
kubeProxy:
  config:
    bindAddress: 0.0.0.0
    ...
kubeletConfiguration:
  baseConfig:
    address: 0.0.0.0
    ...
networking:
  dnsDomain: cluster.local
  podSubnet: ""
  serviceSubnet: 10.96.0.0/12
nodeRegistration:
  criSocket: /var/run/dockershim.sock
  ...
EOF
```

具体可以参考：https://kubernetes.io/zh/docs/reference/setup-tools/kubeadm/kubeadm-init/

接下来我们就可以使用kubeadm init来部署 Master 节点了。



**kubeadm init 的工作流程**

1. Prefligth Checks 检查

   kubeadm 首先要做的，是一系列的检查工作，以确定这台机器可以用来部署 Kubernetes。这一步检查，我们称为“Preflight Checks”，它可以为你省掉很多后续的麻烦。

   其实，Preflight Checks 包括了很多方面，比如：

   - Linux 内核的版本必须是否是 3.10以上？

   - Linux Cgroups 模块是否可用

   - 机器的 hostname 是否标准？在 Kubernetes 项目里，机器的名字以及一切存储在 Etcd 中的 API 对象，都必须使用标准的 DNS 命名（RFC 1123）。

   - 用户安装的 kubeadm 和 kubelet 的版本是否匹配？

   - 机器上是不是已经安装了 Kubernetes 的二进制文件？

   - Kubernetes 的工作端口 10250/10251/10252 端口是不是已经被占用？

   - ip、mount 等 Linux 指令是否存在？

   - Docker 是否已经安装？

   - Docker 和 kubelet 使用的驱动程序是否相同？

   - 。。。。

   上面这些检查中，一些检查项目仅仅触发警告，其它的则会被视为错误并且退出 kubeadm，除非问题得到解决或者用户指定了 --ignore-preflight-errors=<list-of-errors> 参数。

2. 生成自签证书

   在检查通过之后，kubeadm 会为你生成 kubernetes对外提供服务所需的各种证书和对应的目录。

   如果用户已经通过 --cert-dir 配置的证书目录（默认为 /etc/kubernetes/pki）提供了他们自己的 CA 证书以及/或者密钥， 那么将会跳过这个步骤。

   kubernetes 对外提供服务时，除非专门开启“不安全模式”，否则都要通过 HTTPS 才能访问 Kube-apiserver。这就需要为 Kubernetes 集群配置好证书文件。

   kubeadm 为 Kubernetes 项目生成的证书文件都放在 Master 节点的 /etc/kubernetes/pki 目录下。在这个目录下，最主要的证书文件是 ca.crt 和对应的私钥 ca.key。

   此外，用户使用 kubectl 获取容器日志等 streaming 操作时，需要通过 kube-apiserver 向 kubelet 发起请求，这个连接也必须是安全的。kubeadm 为 这一步生成的是 apiserver-kubelet-client.out 文件，对应的私钥是 apiserver-kubelet-client.key。

   除此之外，Kubernetes 集群中还有 Aggregate APIServer 等特性，也需要用到专门的证书，这里我就不再一一列举了。可以参考这个文件：https://www.cnblogs.com/shoufu/p/12963081.html 需要指出的是，你可以选择不让 kubeadm 为你生成这些证书，而是拷贝现有的证书到如下证书的目录里：

   ```shell
   /etc/kubernetes/pki/ca.{crt,key}
   [root@zsf-test pki]# tree
   .
   ├── apiserver.crt
   ├── apiserver-etcd-client.crt
   ├── apiserver-etcd-client.key
   ├── apiserver.key
   ├── apiserver-kubelet-client.crt
   ├── apiserver-kubelet-client.key
   ├── ca.crt
   ├── ca.key
   ├── etcd
   │   ├── ca.crt
   │   ├── ca.key
   │   ├── healthcheck-client.crt
   │   ├── healthcheck-client.key
   │   ├── peer.crt
   │   ├── peer.key
   │   ├── server.crt
   │   └── server.key
   ├── front-proxy-ca.crt
   ├── front-proxy-ca.key
   ├── front-proxy-client.crt
   ├── front-proxy-client.key
   ├── sa.key
   └── sa.pub
   ```

3. 生成其他组件访问 kube-apiserver 所需要的配置文件

   这些文件的路径是：/etc/kubernetes/xxx.conf

   ```shell
   ls /etc/kubernetes/*.conf
   /etc/kubernetes/admin.conf  
   /etc/kubernetes/controller-manager.conf  
   /etc/kubernetes/kubelet.conf  
   /etc/kubernetes/scheduler.conf
   ```

   这些文件里面记录的是，当前这个 Master 节点的服务器地址、监听端口、证书目录等信息。这样，对应的客户端（比如 scheduler，kubelet 等），可以直接加载相应的文件，使用里面的信息与 kube-apiserver建立安全连接。

4. 为 Master 组件生成 Pod 配置文件

   在 Kubernetes 中，有一种特殊的容器启动方法叫做“Static Pod”。它允许你把要部署的 Pod 的 YAML 文件放在一个指定的目录里。这样，当这台机器上的 kubelet 启动时，它会自动检查这个目录，加载所有的 Pod YAML 文件，然后在这台机器上启动它们。

   从这一点也可以看出，kubelet 在 Kubernetes 项目中的地位非常高，在设计上它就是一个完全独立的组件，而其他 Master 组件，则更像是辅助性的系统容器。

   ```shell
   cd /etc/kubernetes/manifests
   ├── etcd.yaml
   ├── kube-apiserver.yaml
   ├── kube-controller-manager.yaml
   └── kube-scheduler.yaml
   ```

   然后 kubeadm 会调用 kubelet 运行这些 yml 文件，等到 Master 容器启动后，kubeadm 会通过检查 localhost:6443/healthz 这个 Master 组件的健康检查 url，等待 Master 组件完全运行起来。

   然后，**kubeadm 就会为集群生成一个 bootstrap token**。在后面，只要持有这个 token，任何一个安装了 kubelet 和 kubadm 的节点，都可以通过 kubeadm join 加入到这个集群当中。

   这个 token 的值和使用方法，会在 kubeadm init 结束后被打印出来。

   **在 token 生成之后，kubeadm 会将 ca.crt 等 Master 节点的重要信息，通过 ConfigMap 的方式保存在 Etcd 当中，供后续部署 Node 节点使用。**这个 ConfigMap 的名字是 cluster-info。

   **kubeadm init 的最后一步，就是安装默认插件。**Kubernetes 默认 kube-proxy 和 DNS 这两个插件是必须安装的。它们分别用来提供整个集群的服务发现和 DNS 功能。其实，这两个插件也只是两个容器镜像而已，所以 kubeadm 只要用 Kubernetes 客户端创建两个 Pod 就可以了。

   

**kubeadm join 的工作流程**

这个流程其实非常简单，kubeadm init 生成 bootstrap token 之后，你就可以在任意一台安装了 kubelet 和 kubeadm 的机器上执行 kubeadm join 了。

可是，为什么执行 kubeadm join 需要这样一个 token 呢？

因为，任何一台机器想要成为 Kubernetes 集群中的一个节点，就必须在集群的 kube-apiserver 上注册。可是，要想跟 apiserver 打交道，这台机器就必须要获取到相应的证书文件（CA 文件）。可是，为了能够一键安装，我们就不能让用户去 Master 节点上手动拷贝这些文件。

所以，kubeadm 至少需要发起一次“不安全模式”的访问到 kube-apiserver，从而拿到保存在 ConfigMap 中的 cluster-info（它保存了 APIServer 的授权信息）。而 bootstrap token，扮演的就是这个过程中的安全验证的角色。

只要有了 cluster-info 里的 kube-apiserver 的地址、端口、证书，kubelet 就可以以“安全模式”连接到 apiserver 上，这样一个新的节点就部署完成了。

接下来，你只要在其他节点上重复这个指令就可以了。

具体安装可以查看文章：

https://zhangguanzhang.github.io/2019/11/24/kubeadm-base-use/#kubeadm%E9%83%A8%E7%BD%B2