```shell
kibana

UAT+PROD环境：http://prod.kibana.cpms.tech/  账号密码： uatuser/uatuser#123
kibana_waf: 10.160.47.104:6601
kibana_slb: 10.160.47.50:6601
kibana_rip: 10.160.58.10:15601

es_waf: 10.160.47.104:19200    账号密码：elastic/changeme#2021!
es_slb: 10.160.47.50:19200
es_rlb: 10.160.58.1:19200

kibana.yml
server.port: 15601
server.host: "10.160.58.10"
server.maxPayloadBytes: 104857600
server.name: "prod-kibana"
elasticsearch.hosts: ["http://10.160.58.9:19200", "http://10.160.58.10:19200", "http://10.160.58.11:19200"]
kibana.index: ".kibana"
kibana.defaultAppId: "home"
elasticsearch.username: "elastic"
elasticsearch.password: "changeme#2021!"
xpack.reporting.csv.maxSizeBytes: 104857600
xpack.reporting.queue.timeout: 1800000
xpack.reporting.encryptionKey: "a_random_string"
xpack.security.encryptionKey: "something_at_least_32_characters"
i18n.locale: "zh-CN"

x-pack
```

