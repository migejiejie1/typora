```shell
elasticsearch.yml
# 为集群使用描述性名称
cluster.name: elk-cluster 
# 节点名称设置, 节点名默认为机器的主机名
node.name: node01
#该节点是否具有成为主节点的资格
node.master: true
# 该节点是否存储数据
node.data: true
# 是否是预处理节点, 后期提供建立索引和查询索引的服务
node.ingest: false
# 存储数据的目录路径
path.data: /data/elasticsearch-7/data 
# 存储日志的目录路径
path.logs: /data/elasticsearch-7/logs 
# 指定用于监听请求的网络接口
network.host: 192.168.1.11
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
# HTTP 请求通信端口。
http.port: 9200 
# 节点间通信端口。
transport.tcp.port: 9300
# HTTP请求的最大内容。 默认为100mb。  
http.max_content_length: 1024mb
# client ping命令响应的时间，如果无返回，则认为此节点不可用。默认5s 
client.transport.ping_timeout: 10s
# 代表忽略连接节点的集群名验证
client.transport.ignore_cluster_name: true
# sample/ping 节点的时间间隔，默认是5s
client.transport.nodes_sampler_interval: 5s
# 设置了最少有多少个备选主节点参加选举
discovery.zen.minimum_master_nodes: 2
# 来控制节点加入某个集群或者开始选举的响应时间
discovery.zen.ping_timeout: 3s #默认3秒
# 判断节点是否脱离, 本节点隔多久向目标节点发送一次ping请求。
discovery.zen.fd.ping_interval: 10s  #默认1s
# 节点等待ping请求的回复时间。
discovery.zen.fd.ping_timeout: 60s   #默认30s
# 在目标节点被认定不可用之前ping请求重试的次数。
discovery.zen.fd.ping_retries: 3  # 默认3
# 提供群集中符合 master 条件且可能处于活动状态且可联系以进行发现过程的其他节点的列表。
discovery.seed_hosts: ["192.168.1.11:9300", "192.168.1.12:9300","192.168.1.13:9300"]
# 显式地列出符合主资格的节点
cluster.initial_master_nodes: ["192.168.1.11:9300", "192.168.1.12:9300","192.168.1.13:9300"]
# 只要有这么多的数据或主节点加入集群，就可以恢复。  
gateway.recover_after_nodes: 2
# 启用跨源资源共享
http.cors.enabled: true
# 允许哪些起源
http.cors.allow-origin: "*"
# 设置最低安全性: 启用密码验证
xpack.security.enabled: true
# 基本安全性: 保护集群中节点之间的所有内部通信。
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
# 7.0以上? 将GeoIp功能默认开启了采集。在默认的启动下是会去官网的默认地址下获取最新的Ip的GEO信息
ingest.geoip.downloader.enabled: false





kibana.yml
server.port: 5601
server.host: "192.168.1.11"
# 指定 Elasticsearch 集群的 HTTP URL。
#elasticsearch.hosts: ["http://192.168.1.11:9200","http://192.168.1.12:9200","http://192.168.1.13:9200"]

kibana.index: ".kibana"
elasticsearch.username: "kibana_system"
i18n.locale: "zh-CN"

# 加密 Kibana 和 Elasticsearch 之间的流量配置: 
# 指定 Elasticsearch 集群的 HTTPS URL。
elasticsearch.hosts: ["https://192.168.1.11:9200","https://192.168.1.12:9200","https://192.168.1.13:9200"]
# 指定 HTTP 层的安全证书的位置
elasticsearch.ssl.certificateAuthorities: /usr/local/kibana-7.16.2-linux-x86_64/config/kibana/elasticsearch-ca.pem

# 加密浏览器和 Kibana 之间的流量: 
# 为入站连接启用 TLS
server.ssl.enabled: true
# 配置 Kibana 以访问服务器证书和未加密的私钥
server.ssl.certificate: /usr/local/kibana-7.16.2-linux-x86_64/config/crt/kibana-server.crt
server.ssl.key: /usr/local/kibana-7.16.2-linux-x86_64/config/crt/kibana-server.key

```

