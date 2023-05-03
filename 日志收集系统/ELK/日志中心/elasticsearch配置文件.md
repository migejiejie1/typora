```shell
================================ elasticsearch-master =====================================
elasticsearch.yml
cluster.name: prod-log
node.name: node-1          //需要修改
path.data: /data/es_data
path.logs: /data/es_logs
network.host: 10.160.58.1       //需要修改
network.tcp.no_delay: true
network.tcp.keep_alive: true
network.tcp.reuse_address: true
network.tcp.send_buffer_size: 128mb
network.tcp.receive_buffer_size: 128mb
http.port: 19200
transport.tcp.port: 19300
discovery.seed_hosts: ["10.160.58.1:19300", "10.160.58.2:19300", "10.160.58.3:19300"]
cluster.initial_master_nodes: ["10.160.58.1:19300", "10.160.58.2:19300", "10.160.58.3:19300"]
discovery.zen.minimum_master_nodes: 2
gateway.recover_after_nodes: 3
http.cors.enabled: true
http.cors.allow-origin: "*"
xpack.security.enabled: true
xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.verification_mode: certificate
xpack.security.transport.ssl.keystore.path: /data/node-1/config/elastic-certificates.p12         //需要修改
xpack.security.transport.ssl.truststore.path: /data/node-1/config/elastic-certificates.p12       //需要修改
node.master: true
node.data: false
node.ingest: false

jvm.options
-Xms16g
-Xmx16g

elastic-certificates.p12
elasticsearch.keystore

================================ elasticsearch-data =====================================
elasticsearch.yml
cluster.name: prod-log
node.name: node-4         //需要修改
path.data: /data/es_data
path.logs: /data/es_logs
network.host: 10.160.58.4    //需要修改
network.tcp.no_delay: true
network.tcp.keep_alive: true
network.tcp.reuse_address: true
network.tcp.send_buffer_size: 128mb
network.tcp.receive_buffer_size: 128mb
http.port: 19200
transport.tcp.port: 19300
discovery.seed_hosts: ["10.160.58.1:19300", "10.160.58.2:19300", "10.160.58.3:19300"]
cluster.initial_master_nodes: ["10.160.58.1:19300", "10.160.58.2:19300", "10.160.58.3:19300"]
discovery.zen.minimum_master_nodes: 2
gateway.recover_after_nodes: 3
http.cors.enabled: true
http.cors.allow-origin: "*"
xpack.security.enabled: true
xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.verification_mode: certificate
xpack.security.transport.ssl.keystore.path: /data/node-4/config/elastic-certificates.p12    //需要修改
xpack.security.transport.ssl.truststore.path: /data/node-4/config/elastic-certificates.p12  //需要修改
node.master: false
node.data: true
node.ingest: false

jvm.options
-Xms16g
-Xmx16g

elastic-certificates.p12
elasticsearch.keystore

================================ elasticsearch-client =====================================
elasticsearch.yml
cluster.name: prod-log
node.name: node-9            //需要修改
path.data: /data/es_data
path.logs: /data/es_logs
network.host: 10.160.58.9      //需要修改
network.tcp.no_delay: true
network.tcp.keep_alive: true
network.tcp.reuse_address: true
network.tcp.send_buffer_size: 128mb
network.tcp.receive_buffer_size: 128mb
http.port: 19200
transport.tcp.port: 19300
discovery.seed_hosts: ["10.160.58.1:19300", "10.160.58.2:19300", "10.160.58.3:19300"]
cluster.initial_master_nodes: ["10.160.58.1:19300", "10.160.58.2:19300", "10.160.58.3:19300"]
discovery.zen.minimum_master_nodes: 2
gateway.recover_after_nodes: 3
http.cors.enabled: true
http.cors.allow-origin: "*"
xpack.security.enabled: true
xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.verification_mode: certificate
xpack.security.transport.ssl.keystore.path: /data/node-9/config/elastic-certificates.p12      //需要修改
xpack.security.transport.ssl.truststore.path: /data/node-9/config/elastic-certificates.p12    //需要修改
node.master: false
node.data: false
node.ingest: true


```

