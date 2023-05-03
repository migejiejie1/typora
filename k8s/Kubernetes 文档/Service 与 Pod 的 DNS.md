# Service 与 Pod 的 DNS

Kubernetes 为 Service 和 Pod 创建 DNS 记录。 你可以使用一致的 DNS 名称而非 IP 地址访问 Service。

## 介绍

Kubernetes DNS 除了在集群上调度 DNS Pod 和 Service， 还配置 kubelet 以告知各个容器使用 DNS Service 的 IP 来解析 DNS 名称。

集群中定义的每个 Service （包括 DNS 服务器自身）都被赋予一个 DNS 名称。 默认情况下，客户端 Pod 的 DNS 搜索列表会包含 Pod 自身的名字空间和集群的默认域。

### Service 的名字空间

DNS 查询可能因为执行查询的 Pod 所在的名字空间而返回不同的结果。 不指定名字空间的 DNS 查询会被限制在 Pod 所在的名字空间内。 要访问其他名字空间中的 Service，需要在 DNS 查询中指定名字空间。

例如，假定名字空间 `test` 中存在一个 Pod，`prod` 名字空间中存在一个服务 `data`。

Pod 查询 `data` 时没有返回结果，因为使用的是 Pod 的名字空间 `test`。

Pod 查询 `data.prod` 时则会返回预期的结果，因为查询中指定了名字空间。

DNS 查询可以使用 Pod 中的 `/etc/resolv.conf` 展开。kubelet 会为每个 Pod 生成此文件。例如，对 `data` 的查询可能被展开为 `data.test.svc.cluster.local`。 `search` 选项的取值会被用来展开查询。要进一步了解 DNS 查询，可参阅 [`resolv.conf` 手册页面](https://www.man7.org/linux/man-pages/man5/resolv.conf.5.html)。

```
nameserver 10.32.0.10
search <namespace>.svc.cluster.local svc.cluster.local cluster.local
options ndots:5
```

概括起来，名字空间 `test` 中的 Pod 可以成功地解析 `data.prod` 或者 `data.prod.svc.cluster.local`。

### DNS 记录

哪些对象会获得 DNS 记录呢？

1. Services
2. Pods

以下各节详细介绍已支持的 DNS 记录类型和布局。 其它布局、名称或者查询即使碰巧可以工作，也应视为实现细节， 将来很可能被更改而且不会因此发出警告。 有关最新规范请查看 [Kubernetes 基于 DNS 的服务发现](https://github.com/kubernetes/dns/blob/master/docs/specification.md)。

### Services

#### A/AAAA 记录

“普通” Service（除了无头 Service）会以 `my-svc.my-namespace.svc.cluster-domain.example` 这种名字的形式被分配一个 DNS A 或 AAAA 记录，取决于 Service 的 IP 协议族。 该名称会解析成对应 Service 的集群 IP。

“无头（Headless）” Service （没有集群 IP）也会以 `my-svc.my-namespace.svc.cluster-domain.example` 这种名字的形式被指派一个 DNS A 或 AAAA 记录， 具体取决于 Service 的 IP 协议族。 与普通 Service 不同，这一记录会被解析成对应 Service 所选择的 Pod IP 的集合。 客户端要能够使用这组 IP，或者使用标准的轮转策略从这组 IP 中进行选择。

#### SRV 记录

Kubernetes 根据普通 Service 或 [Headless Service](https://kubernetes.io/zh-cn/docs/concepts/services-networking/service/#headless-services) 中的命名端口创建 SRV 记录。每个命名端口， SRV 记录格式为 `_my-port-name._my-port-protocol.my-svc.my-namespace.svc.cluster-domain.example`。 普通 Service，该记录会被解析成端口号和域名：`my-svc.my-namespace.svc.cluster-domain.example`。 无头 Service，该记录会被解析成多个结果，及该服务的每个后端 Pod 各一个 SRV 记录， 其中包含 Pod 端口号和格式为 `auto-generated-name.my-svc.my-namespace.svc.cluster-domain.example` 的域名。

## Pods

### A/AAAA 记录

一般而言，Pod 会对应如下 DNS 名字解析：

```
pod-ip-address.my-namespace.pod.cluster-domain.example
```

例如，对于一个位于 `default` 名字空间，IP 地址为 172.17.0.3 的 Pod， 如果集群的域名为 `cluster.local`，则 Pod 会对应 DNS 名称：

`172-17-0-3.default.pod.cluster.local`.

通过 Service 暴露出来的所有 Pod 都会有如下 DNS 解析名称可用：

`pod-ip-address.service-name.my-namespace.svc.cluster-domain.example`.

### Pod 的 hostname 和 subdomain 字段

当前，创建 Pod 时其主机名取自 Pod 的 `metadata.name` 值。

Pod 规约中包含一个可选的 `hostname` 字段，可以用来指定 Pod 的主机名。 当这个字段被设置时，它将优先于 Pod 的名字成为该 Pod 的主机名。 举个例子，给定一个 `hostname` 设置为 "`my-host`" 的 Pod， 该 Pod 的主机名将被设置为 "`my-host`"。

Pod 规约还有一个可选的 `subdomain` 字段，可以用来指定 Pod 的子域名。 举个例子，某 Pod 的 `hostname` 设置为 “`foo`”，`subdomain` 设置为 “`bar`”， 在名字空间 “`my-namespace`” 中对应的完全限定域名（FQDN）为 “`foo.bar.my-namespace.svc.cluster-domain.example`”。

示例：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: default-subdomain
spec:
  selector:
    name: busybox
  clusterIP: None
  ports:
  - name: foo # 实际上不需要指定端口号
    port: 1234
    targetPort: 1234
---
apiVersion: v1
kind: Pod
metadata:
  name: busybox1
  labels:
    name: busybox
spec:
  hostname: busybox-1
  subdomain: default-subdomain
  containers:
  - image: busybox:1.28
    command:
      - sleep
      - "3600"
    name: busybox
---
apiVersion: v1
kind: Pod
metadata:
  name: busybox2
  labels:
    name: busybox
spec:
  hostname: busybox-2
  subdomain: default-subdomain
  containers:
  - image: busybox:1.28
    command:
      - sleep
      - "3600"
    name: busybox
```

如果某无头 Service 与某 Pod 在同一个名字空间中，且它们具有相同的子域名， 集群的 DNS 服务器也会为该 Pod 的全限定主机名返回 A 记录或 AAAA 记录。 例如，在同一个名字空间中，给定一个主机名为 “busybox-1”、 子域名设置为 “default-subdomain” 的 Pod，和一个名称为 “`default-subdomain`” 的无头 Service，Pod 将看到自己的 FQDN 为 "`busybox-1.default-subdomain.my-namespace.svc.cluster-domain.example`"。 DNS 会为此名字提供一个 A 记录或 AAAA 记录，指向该 Pod 的 IP。 “`busybox1`” 和 “`busybox2`” 这两个 Pod 分别具有它们自己的 A 或 AAAA 记录。

Endpoints 对象可以为任何端点地址及其 IP 指定 `hostname`。

**说明：** 由于不是为 Pod 名称创建 A 或 AAAA 记录的，因此 Pod 的 A 或 AAAA 需要 `hostname`。 没有设置 `hostname` 但设置了 `subdomain` 的 Pod 只会为 无头 Service 创建 A 或 AAAA 记录（`default-subdomain.my-namespace.svc.cluster-domain.example`） 指向 Pod 的 IP 地址。 另外，除非在服务上设置了 `publishNotReadyAddresses=True`，否则只有 Pod 进入就绪状态 才会有与之对应的记录。

### Pod 的 setHostnameAsFQDN 字段

**特性状态：** `Kubernetes v1.22 [stable]`

当 Pod 配置为具有全限定域名 (FQDN) 时，其主机名是短主机名。 例如，如果你有一个具有完全限定域名 `busybox-1.default-subdomain.my-namespace.svc.cluster-domain.example` 的 Pod， 则默认情况下，该 Pod 内的 `hostname` 命令返回 `busybox-1`，而 `hostname --fqdn` 命令返回 FQDN。

当你在 Pod 规约中设置了 `setHostnameAsFQDN: true` 时，kubelet 会将 Pod 的全限定域名（FQDN）作为该 Pod 的主机名记录到 Pod 所在名字空间。 在这种情况下，`hostname` 和 `hostname --fqdn` 都会返回 Pod 的全限定域名。

**说明：**

在 Linux 中，内核的主机名字段（`struct utsname` 的 `nodename` 字段）限定 最多 64 个字符。

如果 Pod 启用这一特性，而其 FQDN 超出 64 字符，Pod 的启动会失败。 Pod 会一直出于 `Pending` 状态（通过 `kubectl` 所看到的 `ContainerCreating`）， 并产生错误事件，例如 "Failed to construct FQDN from Pod hostname and cluster domain, FQDN `long-FQDN` is too long (64 characters is the max, 70 characters requested)." （无法基于 Pod 主机名和集群域名构造 FQDN，FQDN `long-FQDN` 过长，至多 64 字符，请求字符数为 70）。 对于这种场景而言，改善用户体验的一种方式是创建一个 [准入 Webhook 控制器](https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/extensible-admission-controllers/#admission-webhooks)， 在用户创建顶层对象（如 Deployment）的时候控制 FQDN 的长度。

### Pod 的 DNS 策略

DNS 策略可以逐个 Pod 来设定。目前 Kubernetes 支持以下特定 Pod 的 DNS 策略。 这些策略可以在 Pod 规约中的 `dnsPolicy` 字段设置：

- "`Default`": Pod 从运行所在的节点继承名称解析配置。参考 [相关讨论](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/dns-custom-nameservers) 获取更多信息。
- "`ClusterFirst`": 与配置的集群域后缀不匹配的任何 DNS 查询（例如 "www.kubernetes.io"） 都将转发到从节点继承的上游名称服务器。集群管理员可能配置了额外的存根域和上游 DNS 服务器。 参阅[相关讨论](https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/dns-custom-nameservers) 了解在这些场景中如何处理 DNS 查询的信息。
- "ClusterFirstWithHostNet"：对于以 hostNetwork 方式运行的 Pod，应显式设置其 DNS 策略 "ClusterFirstWithHostNet"。
  - 注意：这在 Windows 上不支持。 有关详细信息，请参见[下文](https://kubernetes.io/zh-cn/docs/concepts/services-networking/dns-pod-service/#dns-windows)。
- "`None`": 此设置允许 Pod 忽略 Kubernetes 环境中的 DNS 设置。Pod 会使用其 `dnsConfig` 字段 所提供的 DNS 设置。 参见 [Pod 的 DNS 配置](https://kubernetes.io/zh-cn/docs/concepts/services-networking/dns-pod-service/#pod-dns-config)节。

**说明：** "Default" 不是默认的 DNS 策略。如果未明确指定 `dnsPolicy`，则使用 "ClusterFirst"。

下面的示例显示了一个 Pod，其 DNS 策略设置为 "`ClusterFirstWithHostNet`"， 因为它已将 `hostNetwork` 设置为 `true`。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: busybox
  namespace: default
spec:
  containers:
  - image: busybox:1.28
    command:
      - sleep
      - "3600"
    imagePullPolicy: IfNotPresent
    name: busybox
  restartPolicy: Always
  hostNetwork: true
  dnsPolicy: ClusterFirstWithHostNet
```

### Pod 的 DNS 配置

**特性状态：** `Kubernetes v1.14 [stable]`

Pod 的 DNS 配置可让用户对 Pod 的 DNS 设置进行更多控制。

`dnsConfig` 字段是可选的，它可以与任何 `dnsPolicy` 设置一起使用。 但是，当 Pod 的 `dnsPolicy` 设置为 "`None`" 时，必须指定 `dnsConfig` 字段。

用户可以在 `dnsConfig` 字段中指定以下属性：

- `nameservers`：将用作于 Pod 的 DNS 服务器的 IP 地址列表。 最多可以指定 3 个 IP 地址。当 Pod 的 `dnsPolicy` 设置为 "`None`" 时， 列表必须至少包含一个 IP 地址，否则此属性是可选的。 所列出的服务器将合并到从指定的 DNS 策略生成的基本名称服务器，并删除重复的地址。
- `searches`：用于在 Pod 中查找主机名的 DNS 搜索域的列表。此属性是可选的。 指定此属性时，所提供的列表将合并到根据所选 DNS 策略生成的基本搜索域名中。 重复的域名将被删除。Kubernetes 最多允许 6 个搜索域。
- `options`：可选的对象列表，其中每个对象可能具有 `name` 属性（必需）和 `value` 属性（可选）。 此属性中的内容将合并到从指定的 DNS 策略生成的选项。 重复的条目将被删除。

以下是具有自定义 DNS 设置的 Pod 示例：

[`service/networking/custom-dns.yaml`](https://raw.githubusercontent.com/kubernetes/website/main/content/zh-cn/examples/service/networking/custom-dns.yaml) ![Copy service/networking/custom-dns.yaml to clipboard](https://d33wubrfki0l68.cloudfront.net/0901162ab78eb4ff2e9e5dc8b17c3824befc91a6/44ccd/images/copycode.svg)

```yaml
apiVersion: v1
kind: Pod
metadata:
  namespace: default
  name: dns-example
spec:
  containers:
    - name: test
      image: nginx
  dnsPolicy: "None"
  dnsConfig:
    nameservers:
      - 1.2.3.4
    searches:
      - ns1.svc.cluster-domain.example
      - my.dns.search.suffix
    options:
      - name: ndots
        value: "2"
      - name: edns0
```

创建上面的 Pod 后，容器 `test` 会在其 `/etc/resolv.conf` 文件中获取以下内容：

```
nameserver 1.2.3.4
search ns1.svc.cluster-domain.example my.dns.search.suffix
options ndots:2 edns0
```

对于 IPv6 设置，搜索路径和名称服务器应按以下方式设置：

```shell
kubectl exec -it dns-example -- cat /etc/resolv.conf
```

输出类似于：

```
nameserver 2001:db8:30::a
search default.svc.cluster-domain.example svc.cluster-domain.example cluster-domain.example
options ndots:5
```

#### 扩展 DNS 配置

**特性状态：** `Kubernetes 1.22 [alpha]`

对于 Pod DNS 配置，Kubernetes 默认允许最多 6 个 搜索域（ Search Domain） 以及一个最多 256 个字符的搜索域列表。

如果启用 kube-apiserver 和 kubelet 的特性门控 `ExpandedDNSConfig`，Kubernetes 将可以有最多 32 个 搜索域以及一个最多 2048 个字符的搜索域列表。

## Windows 节点上的 DNS 解析

- 在 Windows 节点上运行的 Pod 不支持 ClusterFirstWithHostNet。 Windows 将所有带有 `.` 的名称视为全限定域名（FQDN）并跳过全限定域名（FQDN）解析。
- 在 Windows 上，可以使用的 DNS 解析器有很多。 由于这些解析器彼此之间会有轻微的行为差别，建议使用 [`Resolve-DNSName`](https://docs.microsoft.com/powershell/module/dnsclient/resolve-dnsname) powershell cmdlet 进行名称查询解析。
- 在 Linux 上，有一个 DNS 后缀列表，当解析全名失败时可以使用。 在 Windows 上，你只能有一个 DNS 后缀， 即与该 Pod 的命名空间相关联的 DNS 后缀（例如：`mydns.svc.cluster.local`）。 Windows 可以解析全限定域名（FQDN），和使用了该 DNS 后缀的 Services 或者网络名称。 例如，在 `default` 命名空间中生成一个 Pod，该 Pod 会获得的 DNS 后缀为 `default.svc.cluster.local`。 在 Windows 的 Pod 中，你可以解析 `kubernetes.default.svc.cluster.local` 和 `kubernetes`， 但是不能解析部分限定名称（`kubernetes.default` 和 `kubernetes.default.svc`）。