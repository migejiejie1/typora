```shell
# ======================== Elasticsearch Configuration =========================
#
# NOTE: Elasticsearch comes with reasonable defaults for most settings.
#       Before you set out to tweak and tune the configuration, make sure you
#       understand what are you trying to accomplish and the consequences.
#
# The primary way of configuring a node is via this file. This template lists
# the most important settings you may want to configure for a production cluster.
#
# Please see the documentation for further information on configuration options:
# <http://www.elastic.co/guide/en/elasticsearch/reference/current/setup-configuration.html>
#
# ---------------------------------- Cluster -----------------------------------
#
# Use a descriptive name for your cluster:
#
 cluster.name: PGAW
#
# ------------------------------------ Node ------------------------------------
#
# Use a descriptive name for the node:
#
 node.name: ${HOSTNAME}-data1
 
 node.master: true  #是否可以成为master 
 node.data: true     #是否可以成为data-node

#
# Add custom attributes to the node:
#
# node.rack: r1
#
# ----------------------------------- Paths ------------------------------------
#
# Path to directory where to store the data (separate multiple locations by comma):
#
# path.data: /path/to/data
#
# Path to log files:
#
# path.logs: /path/to/logs
#
# ----------------------------------- Memory -----------------------------------
#
# Lock the memory on startup:
#
#mlockall 属性可以让ES节点不进行Swapping。（注意仅适用于Linux／Unix系统）
#bootstrap.memory_lock: true

#
# Make sure that the `ES_HEAP_SIZE` environment variable is set to about half the memory
# available on the system and that the owner of the process is allowed to use this limit.
#
# Elasticsearch performs poorly when the system is swapping the memory.
#
# ---------------------------------- Network -----------------------------------
#
# Set the bind address to a specific IP (IPv4 or IPv6):
#
 network.host: 10.51.13.123

#
# Set a custom port for HTTP:
#注意同一机器上不同es实例使用不同端口
 transport.tcp.port: 9310
 http.port: 8210

#
# For more information, see the documentation at:
# <http://www.elastic.co/guide/en/elasticsearch/reference/current/modules-network.html>
#
# --------------------------------- Discovery ----------------------------------
#
# Pass an initial list of hosts to perform discovery when new node is started:
# The default list of hosts is ["127.0.0.1", "[::1]"]
#
# discovery.zen.ping.unicast.hosts: ["host1", "host2"]
#
# Prevent the "split brain" by configuring the majority of nodes (total number of nodes / 2 + 1):
#
#集群要求最少master节点数量，大于master总数的一半，防止脑裂
 discovery.zen.minimum_master_nodes: 2

 discovery.zen.ping.timeout: 3s #节点发现同步超时时间

 discovery.zen.ping.multicast.enabled: false #禁止多播发现

#配置单播发现的节点列表，将所有master节点配置上即可，注意是tcp端口号
 discovery.zen.ping.unicast.hosts: ["10.51.13.123:9310", "10.51.13.123:9410","10.51.13.123:9510"]

#
# For more information, see the documentation at:
# <http://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery.html>
#
# ---------------------------------- Gateway -----------------------------------
#
# Block initial recovery after a full cluster restart until N nodes are started:
#恢复任务：在线的最少节点数，设置为总data-node的数量
 gateway.recover_after_nodes: 3

#恢复任务：满足在线节点数后，还需在此时间过后才可初始化恢复任务
 gateway.recover_after_time: 5m

#恢复任务：满足在线节点数，总节点数达到此值不需等待超时时间就可立即启动恢复， 设置为总node个数
 gateway.expected_nodes: 45

################################## GC Logging ################################
##监控垃圾回收时的用时，超过对应上线会通过日志打印
#monitor.jvm.gc.young.warn: 1000ms
#monitor.jvm.gc.young.info: 700ms
#monitor.jvm.gc.young.debug: 400ms
#monitor.jvm.gc.old.warn: 10s
#monitor.jvm.gc.old.info: 5s
#monitor.jvm.gc.old.debug: 2s

#
# For more information, see the documentation at:
# <http://www.elastic.co/guide/en/elasticsearch/reference/current/modules-gateway.html>
#
# ---------------------------------- Various -----------------------------------
#
# Disable starting multiple nodes on a single system:
#
# node.max_local_storage_nodes: 1
#
# Require explicit names when deleting indices:
#
# action.destructive_requires_name: true
#
 http.cors.enabled: true
 http.cors.allow-origin: "*"


```

