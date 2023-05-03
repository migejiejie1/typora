```shell
# ======================== Elasticsearch Configuration ========================= 
# 配置节点的主要方式是通过这个文件。
# https://www.elastic.co/guide/en/elasticsearch/reference/index.html

# ================================ cluster,node name配置 ================================
# 为集群使用描述性名称
cluster.name: Elk-Cluster
# 节点名称设置, 节点名默认为机器的主机名
node.name: node01 

# ================================ master, data, client节点配置 ================================
# 该节点是否具有成为主节点的资格,建议集群中设置3台以上的节点作为master节点，这些节点只负责成为主节点，维护整个集群的状态。
node.master: true
# 该节点是否存储数据, 根据数据量设置一批data节点，这些节点只负责存储数据。
node.data: true
# 执行预处理管道，不负责数据和集群相关的事物。它在索引之前预处理文档，拦截文档的bulk(主体)和index请求，然后加以转换。
# 将文档传回给bulk和index API，用户可以定义一个管道，指定一系列的预处理器。
# Ingest 节点的基础原理是: 节点接收到数据之后，根据请求参数中指定的管道流 id，
# 找到对应的已注册管道流，对数据进行处理，然后将处理过后的数据，按照 Elasticsearch 标准的 indexing 流程继续运行。
node.ingest: false

# ======================== master,data,client,ingest配置说明 ========================
# master - 主节点配置
# 主要功能：维护元数据，管理集群节点状态；不负责数据写入和查询。
# 配置要点：内存可以相对小一些，但是机器一定要稳定，最好是独占的机器。
node.master: true
node.data: false
# data - 数据节点配置
# 主要功能：负责数据的写入与查询，压力大。
# 配置要点：大内存，最好是独占的机器。
node.master: false
node.data: true
# client - 客户端节点配置
# 主要功能：负责任务分发和结果汇聚，分担数据节点压力。
# 配置要点：大内存，最好是独占的机器
node.master: false
node.data: false
# ingest - 预处理节点配置
node.ingest: true

# =============================== ElasticSearch冷热分离架构 ===========================
# 冷热数据分离的关键点为使用固态磁盘存储数据。
# 将实时数据(5天内)存储到热节点中，历史数据(5天前)的存储到冷节点中, 根据时间将热节点的数据迁移到冷节点
# 冷热分离架构相比普通的data节点, 主要是增加了这两个配置:
# 向节点添加自定义属性:
# 热节点配置示例
node.attr.rack: r1
node.attr.box_type: hot
# 冷节点配置配置示例
node.attr.rack: r9
node.attr.box_type: cool

# =============================== 数据,日志存储配置 ====================================
# 存储数据的目录路径(用逗号分隔多个位置):  不要尝试对数据目录进行文件系统备份;没有受支持的方法来还原此类备份。而应使用快照和还原来安全地进行备份。
# 可以指定多个路径。Elasticsearch 在所有提供的路径中存储节点的数据，但将每个分片的数据保留在同一路径上。
# Elasticsearch 不会在节点的数据路径之间平衡分片。单个路径中的高磁盘使用率可能会触发整个节点的高磁盘使用率水位线。
# 如果触发，Elasticsearch 不会向节点添加分片，即使节点的其他路径具有可用磁盘空间也是如此。如果需要额外的磁盘空间，我们建议您添加新节点而不是其他数据路径。
# 对多个数据路径的支持在 7.13 中已弃用，并将在将来的版本中移除。
# 存储数据的目录路径
path.data: /data/elasticsearch/data 
# 存储日志的目录路径
path.logs: /data/elasticsearch/logs 

# =============================== es启动时一些参数配置 =================================
# 确保堆大小设置为系统可用内存的一半左右，并且允许进程的所有者使用这个限制。  
# 当系统交换内存时，Elasticsearch的性能很差。
# 启动时锁定内存
bootstrap.memory_lock: true 
# 启动时检测系统调用过滤器
bootstrap.system_call_filter: false

# =============================== es 监听配置====================================
# 默认情况下，Elasticsearch 仅绑定到环回地址, 这足以在单个服务器上运行包含一个或多个节点的群集以进行开发和测试，通常您只需要配置：127.0.0.1 [::1]
# 当您为 network.host 提供值时，Elasticsearch 假定您正在从开发模式切换到生产模式，并将许多系统启动检查从警告升级到异常。
# 节点将绑定到一个 主机名或者 ip地址 并且会将该这个节点通知集群中的其他节点。接受 ip 地址，主机名，指定值或者包含这些值的数组
network.host: 0.0.0.0
# network.host 的特殊值
# 以下特殊值将可以传递给 network.host：
# 1) network Interface 网络接口的地址，例如 en0。
# 2) local 系统中的回环地址，例如 127.0.0.1。
# 3) site 系统中任何的本地站点地址，例如 192.168.0.1。
# 4) global 系统中的任何全局作用域地 8.8.8.8。
# 
# IPv4 vs IPv6
# 默认情况下这些特殊值都可以在 IPv4 和 IPv6 中使用，但是你可以使用 :ipv4，:ipv6 字符限制使用。
# 例如，en0:ipv4 将绑定 en0 接口的 IPv4 地址。
#
# 高级网络配置
# 在常用的网络配置中解释的 network.host 是快捷方式，同时设置绑定地址和发布地址。在高级使用情况下，例如在一个代理服务器中运行，你可能需要设置如下不同的值：
# 1) 这将指定用于监听请求的网络接口。一个节点可以绑定多个接口，例如有两块网卡，一个本地站点地址，一个本地地址。 默认值：network.host
network.bind_host: 0.0.0.0
# 2) 发布地址，一个单一地址，用于通知集群中的其他节点，以便其他的节点能够和它通信。当前，一个 elasticsearch 节点可能被绑定到多个地址，但是仅仅有一个发布地址。
# 如果没有指定，这个默认值将为 network.host 配置中的最好地址，以 IPv4/Ipv6 堆栈性能，之后以稳定性排序。
network.publish_host: 192.168.0.1
# 上述两个设置可以向 network.host 那样被设置--他们都接受 IP 地址，主机名和特定值

# =============================== es 高级tcp配置====================================
# 高级 TCP 设置,任何使用 TCP（像 HTTP 和 Transport 模块）共享如下设置：
# 开启或关闭 TCP 无延迟设置。默认值为 true。
network.tcp.no_delay: true
# 开启或关闭 TCP 长连接，默认值为 true。
network.tcp.keep_alive: true
# 一个地址是否可以被重用。在非 windows 机子上默认值为 true。
network.tcp.reuse_address: true
#  TCP 发送缓冲区大小（以size unit指定）。没有默认值。
network.tcp.send_buffer_size: 128mb
#  TCP 接收缓冲区大小（以size unit指定）。没有默认值。
network.tcp.receive_buffer_size: 128mb
#
# Transport 和 HTTP 协议
# 一个Elasticsearch节点暴露两个网络协议配置继承上面的设置，但可独立地进一步配置两个网络协议：
# HTTP 请求通信端口。接收单值或者一个范围。如果指定一个范围，该节点将会绑定范围的第一个空闲端口。在这里设置一个特定的HTTP端口:
http.port: 9200 
# http.port: 9200-9300
# 节点间通信端口。接收单值或者一个范围。如果指定一个范围，该节点将会绑定范围的第一个空闲端口。
transport.tcp.port: 9300
# transport.tcp.port: 9300-9400
#
# HTTP请求的最大内容。 默认为100mb。  
http.max_content_length: 1024mb

# =============================== transport客户端设置 ===============================
# 等待节点的ping响应的时间, 默认5s 
# client ping命令响应的时间，如果无返回，则认为此节点不可用。如果客户端和集群间网络延迟较大或者连接不稳定，可能需要调大这个值。 
client.transport.ping_timeout: 60s
# 代表忽略连接节点的集群名验证
client.transport.ignore_cluster_name: true
# sample/ping 节点的时间间隔，默认是5s
client.transport.nodes_sampler_interval: 5s

# =============================== 自动创建索引配置 ===============================
# 默认情况下，Elasticsearch 配置为允许自动创建索引, 如果您在 Elasticsearch 中禁用了自动创建索引, 允许商业功能创建以下索引
action.auto_create_index: *
# action.auto_create_index: .monitoring*,.watches,.triggered_watches,.watcher-history*,.ml*

# =============================== es安全通信配置 ===============================
# Elasticsearch安全特性默认情况下是不启用的。  
# 这些功能是免费的，但需要更改配置才能启用。这意味着用户不需要提供凭证，可以获得完全的访问权指向集群。 网络连接也没有加密。  
# https://www.elastic.co/guide/en/elasticsearch/reference/7.16/configuring-stack-security.html
# 设置最低安全性, 启用密码验证
xpack.security.enabled: true
# 基本安全性, 保护集群中节点之间的所有内部通信。
xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.verification_mode: certificate 
xpack.security.transport.ssl.client_authentication: required
xpack.security.transport.ssl.keystore.path: elastic-certificates.p12
xpack.security.transport.ssl.truststore.path: elastic-certificates.p12
# 基本安全性加安全 HTTPS 流量（Elastic stack）, 启用 HTTPS 安全性并指定安全证书的位置。
xpack.security.http.ssl.enabled: true
xpack.security.http.ssl.keystore.path: /usr/local/elasticsearch-7.16.2/config/elasticsearch/node1/http.p12
# 启用监控收集
xpack.monitoring.collection.enabled: true

# =============================== 分片和副本 ===============================
# 以前版本的 Elasticsearch 默认为每个索引创建五个分片。从 7.0.0 开始，默认值是每个索引一个分片。
# 分片数
index.number_of_shards: 5 
# 默认一个主分片配有一个副本
index.number_of_replicas: 1 

# =============================== Zen发现机制配置 ===============================
# 控制节点 加入某个集群 或者 开始选举 的响应时间。#默认3秒
# 在这段时间内有3个 ping 会发出。如果超时,重新启动 ping 程序。在网络缓慢或拥塞时，3秒时间可能不够，这种情况下，需要慎重增加超时时间，增加超时时间会减慢选举过程。
discovery.zen.ping_timeout: 150s 
# Zen发现机制的故障检测
# ElasticSearch运行时会启动两个探测进程。一个进程用于从主节点向集群中其它节点发送ping请求来检测节点是否正常可用。
# 另一个进程的工作反过来了，其它的节点向主节点发送ping请求来验证主节点是否正常且忠于职守。
# 判断节点是否脱离, 本节间隔多久向目标节点发送一次ping请求。默认1s
discovery.zen.fd.ping_interval: 10s  
# 节点等待ping请求的回复时间。如果节点百分百可用或者网络比较慢，可能就需要增加该属性值。默认30s
discovery.zen.fd.ping_timeout: 60s   
# 在目标节点被认定不可用之前ping请求重试的次数。用户可以在网络丢包比较严重的网络状况下增加该属性值(或者修复网络状况)。默认3次
discovery.zen.fd.ping_retries: 10  
#
# 以下值为建议值: 
# 这些参数在我们的环境长期运行后验证基本是比较理想的。 只有负载最重的日志集群，在夜间做force merge的时候，因为某些shard过大(300 - 400GB)， 
# 大量的IO操作因为机器load过高，偶尔出现节点被误判脱离，然后马上又加回的现象。 
# 虽然继续增大下面的几个参数可以减少误判的机会，但是如果真的有节点故障，将其剔除掉的周期又太长。 
# 所以我们还是通过增加shard数量，限制shard的size来缓解force merge带来的压力，降低高负载节点被误判脱离的几率。 
# 文档 "discovery.zen.ping_timeout 参数作用的疑惑和探究" https://elasticsearch.cn/question/4199
discovery.zen.ping_timeout: 3s #默认3秒
discovery.zen.fd.ping_interval: 10s  #默认1s
discovery.zen.fd.ping_timeout: 60s   #默认30s
discovery.zen.fd.ping_retries: 3  # 默认3次
#
#在一个节点准备加入集群时，会向msater节点发送请求，这里有个超时时间为timeout的20倍（默认情况下），从而确定是否加入成功。
discovery.zen.join_timeout: 60s

# =============================== 跨域配置 ===============================
# 启用或禁用跨源资源共享，例如，在另一个源上的浏览器是否可以执行针对Elasticsearch的请求。设置为true启用Elasticsearch来处理飞行前的CORS请求。
# 如果http.cors允许请求中发送的Origin, Elasticsearch将用Access-Control-Allow-Origin报头响应这些请求。allow-origin列表。
# 设置为false(默认值)使Elasticsearch忽略Origin请求头，有效地禁用CORS请求，因为Elasticsearch永远不会响应Access-Control-Allow-Origin响应头。
# 请注意，如果客户端不发送带有Origin报头的prefliaht请求，或者它不检查来自服务器的响应报头以验证Access Control-Allow-Origin响应报头，
# 那么跨源安全性就会受到损害。如果Elasticsearch没有启用CORS，那么客户端知道的唯一方法就是发送一个pre-flight请求，并意识到所需的响应头丢失了。
http.cors.enabled: true
# 允许哪些起源。默认为不允许的起源。如果你在值前添加/，它将被视为一个正则表达式，
# 允许你支持HTTP和HTTPs。例如使用/https?: \ / / localhost (: 10 - 91 +) ?/将在两种情况下适当地返回请求头。
# *是一个有效的值，但被认为是一个安全风险，因为您的Elasticsearch实例是开放的，以跨来源的请求从任何地方。
http.cors.allow-origin: "*"

# =============================== master节点选举 ===============================
# 设置了最少有多少个备选主节点参加选举，同时也设置了一个主节点需要控制最少多少个备选主节点才能继续保持主节点身份。
# 如果控制的备选主节点少于 discovery.zen.minimum_master_nodes 个，那么当前主节点下台，重新开始选举。
# 只有足够的master候选节点时，才可以选举出一个master。该参数必须设置为集群中master候选节点的quorum数量。
# quorum的算法=master候选节点数量/2+1
# 此项设置还会影响到集群状态的update,当主节点收到来自其他节点的状态更新数据，更新数据是否接受前，
# 必须由大于此设置的 master-eligible 数量承认，才说明此集群的更新成功，才会同步到其他所有的节点。
discovery.zen.minimum_master_nodes: 2

# =============================== 配置主机发现列表 ===============================
# 7.0中已弃用, 低版本适用
# Elasticsearch的默认发现模块是Zen discovery。
# 有两种方法可用于配置主机节点列表：单播和基于文件, 建议主机节点列表主要由集群中那些 Master-eligible (候选主) 的节点组成。
# Master-eligible: node.master 参数设置为 true（默认）的节点，使其有资格被选为控制集群的主节点。
#
# 1) 单播
# 提供初始其他节点列表，当前节点将尝试联系。
discovery.zen.ping.unicast.hosts: ["host1:port", "host2:9300", "host2:[9300-9400]"]
# 该属性指定节点发现模块能够开启的单播最大并发连接数。默认值: 10
discovery.zen.ping.unicats.concurrent_connects: 10
# 配置在每轮ping操作中等待DNS查找的时间, 需要指定时间单位，默认为5秒。
discovery.zen.ping.unicast.resolve_timeout: 5s
#
# 2) 基于文件
# 通过外部文件配置主机列表
discovery.zen.hosts_provider: file
#
# 7.0以上配置
# 1) 单播 
# 此设置提供群集中符合 master 条件且可能处于活动状态且可联系以进行发现过程的其他节点的列表。
# 此设置接受群集中所有符合主节点条件的节点的 YAML序列 或 地址数组。每个地址可以是一个 IP 地址，也可以是通过 DNS 解析为一个或多个 IP 地址的主机名。
# 提供集群中合格主节点的列表。每个值都有 host:port 或 host 的格式,其中port默认为设置 transport.profiles.default.port, 如果未设置则返回 transport.tcp.port。 
# 请注意，IPv6主机必须用括号括起来。 默认值为["127.0.0.1"，"[::1]"]。 
# 当节点不是集群的一部分时(比如节点重启，启动或者由于某些错误从集群中断开)，节点会发送一个ping请求到事先设置好的地址中，来通知集群它已经准备好加入到集群中了。
# 因此用于单播节点发现的主机列表基本上不必是集群中的所有节点，因为一个节点一旦连接到集群中的一个节点，这个连接信息就会发送集群中其它所有的节点。
discovery.seed_hosts: ["host1:port", "host2:9300", "host2:[9300-9400]"]
# 2) 基于文件
# 基于文件的种子主机提供, 程序通过外部文件配置主机列表。
# Elasticsearch会在文件更改时重新加载该文件，以便种子节点列表可以动态更改，而无需重新启动每个节点。
# 例如，这为在Docker容器中运行的Elasticsearch实例提供了一种方便的机制，可以动态提供一个IP地址列表，以便在节点启动时无法知道这些IP地址时连接到Zen discovery。
# 然后在 $ES_PATH_CONF/unicast_hosts.txt 单播主机上创建一个文件。TXT格式如下所示。
  192.168.1.5
  192.168.1.6:9300
  192.168.1.7:10005
  # an IPv6 address
  [2001:0db8:85a3:0000:0000:8a2e:0370:7334]:9301
# 如果未指定端口号，则使用默认值9300。允许使用主机名而不止IP地址。必须在括号中指定IPv6地址，并在括号后面添加端口。
# 任何时候当 unicast_hosts.txt 文件发生变化时，新的变化将被Elasticsearch选中，新的主机列表将被使用。
discovery.seed_providers: file
#
# 首次启动 Elasticsearch 集群时，集群引导步骤会确定在 第一次选举中计入其投票的符合主节点条件的节点集:
# 当您第一次启动一个全新的Elasticsearch集群时，会有一个集群引导步骤，它确定一组符合主条件的节点，这些节点的选票在第一次选举中被统计。 
# 在开发模式中，没有配置发现设置，这个步骤由节点自己自动执行。 
# 由于这种自动引导在本质上是不安全的，所以当您在生产模式下启动一个全新的集群时，必须显式地列出符合主资格的节点，
# 这些节点的选票应该在第一次选举中被统计。 
cluster.initial_master_nodes: ["node-1:9300", "node-2:9300"]
# 只要有这么多的数据或主节点加入集群，就可以恢复。  
gateway.recover_after_nodes: 3

# =============================== 其他配置 ===============================
#指的是集群在选举master时，不考虑非master-eligible的选票。默认值是false
discovery.zen.master_election.ignore_non_master_pings: true
# 是否启用多播, 默认启用, 单播发现允许显式地控制哪个节点将被用来发现集群, 它可以在组播不存在的情况下使用，也可以用于限制集群的通信。
discovery.zen.ping.multicast.enabled: true
# 删除索引时要求显式名称:
action.destructive_requires_name: true

# =============================== master选举的过程===============================
# node会对配置文件中 <discovery.zen.ping.unicast.hosts> 的IP列表发送unicast(单播)消息，并通过multicast(多播)的方式寻找集群中的其他节点；
# 当发现更多节点后，再逐个连接，并询问它们知道的集群节点信息, 然后对自己未知的节点发送请求，请求连接。
# 通过这种方式，先前运行的实例可以帮助快速获取群集中当前节点的新的实例。

# 禁用GeoIpDownloader
# 7.0以上? 此版本将GeoIp功能默认开启了采集。在默认的启动下是会去官网的默认地址下获取最新的Ip的GEO信息
ingest.geoip.downloader.enabled: false
```



