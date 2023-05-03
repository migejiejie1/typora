```shell

es node1

elasticsearch.yml
cluster.name: server-log
node.name: node-1
path.data: /data/node-1/es_data
path.logs: /data/node-1/es_logs
network.host: 10.160.58.102
network.tcp.no_delay: true
network.tcp.keep_alive: true
network.tcp.reuse_address: true
network.tcp.send_buffer_size: 128mb
network.tcp.receive_buffer_size: 128mb
http.port: 19200
transport.tcp.port: 19300
discovery.seed_hosts: ["10.160.58.102:19300", "10.160.58.103:19300", "10.160.58.104:19300"]
cluster.initial_master_nodes: ["10.160.58.102:19300", "10.160.58.103:19300", "10.160.58.104:19300"]
http.cors.enabled: true
http.cors.allow-origin: "*"
xpack.monitoring.collection.enabled: true
xpack.security.enabled: true
xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.verification_mode: certificate
xpack.security.transport.ssl.keystore.path: /data/node-1/config/elastic-certificates.p12
xpack.security.transport.ssl.truststore.path: /data/node-1/config/elastic-certificates.p12
node.master: true
node.data: true

jvm.options
-Xms24g
-Xmx24g
============================================================================================================
es node2

elasticsearch.yml
cluster.name: server-log
node.name: node-2
path.data: /data/node-2/es_data
path.logs: /data/node-2/es_logs
network.host: 10.160.58.103
network.tcp.no_delay: true
network.tcp.keep_alive: true
network.tcp.reuse_address: true
network.tcp.send_buffer_size: 128mb
network.tcp.receive_buffer_size: 128mb
http.port: 19200
transport.tcp.port: 19300
discovery.seed_hosts: ["10.160.58.102:19300", "10.160.58.103:19300", "10.160.58.104:19300"]
cluster.initial_master_nodes: ["10.160.58.102:19300", "10.160.58.103:19300", "10.160.58.104:19300"]
http.cors.enabled: true
http.cors.allow-origin: "*"
xpack.monitoring.collection.enabled: true
xpack.security.enabled: true
xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.verification_mode: certificate
xpack.security.transport.ssl.keystore.path: /data/node-2/config/elastic-certificates.p12
xpack.security.transport.ssl.truststore.path: /data/node-2/config/elastic-certificates.p12
node.master: true
node.data: true

jvm.options
-Xms24g
-Xmx24g

============================================================================================================
es node3

elasticsearch.yml
cluster.name: server-log
node.name: node-3
path.data: /data/node-3/es_data
path.logs: /data/node-3/es_logs
network.host: 10.160.58.104
network.tcp.no_delay: true
network.tcp.keep_alive: true
network.tcp.reuse_address: true
network.tcp.send_buffer_size: 128mb
network.tcp.receive_buffer_size: 128mb
http.port: 19200
transport.tcp.port: 19300
discovery.seed_hosts: ["10.160.58.102:19300", "10.160.58.103:19300", "10.160.58.104:19300"]
cluster.initial_master_nodes: ["10.160.58.102:19300", "10.160.58.103:19300", "10.160.58.104:19300"]
http.cors.enabled: true
http.cors.allow-origin: "*"
xpack.monitoring.collection.enabled: true
xpack.security.enabled: true
xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.verification_mode: certificate
xpack.security.transport.ssl.keystore.path: /data/node-3/config/elastic-certificates.p12
xpack.security.transport.ssl.truststore.path: /data/node-3/config/elastic-certificates.p12
node.master: true
node.data: true

jvm.options
-Xms24g
-Xmx24g

```

