```shell
rsyslog

rsyslog.conf
$template jsonFormat,"{\"time\":\"%timereported:::date-rfc3339=seconds%\",\"msg\":\"%msg:::json%\",\"fromhost\":\"%HOSTNAME:::json%\",\"facility\":\"%syslogfacility-text%\",\"priority\":\"%syslogpriority-text%\"}\n"

*.info;mail.none;authpriv.none;cron.none;local1.none    /var/log/messages
authpriv.*                                              /var/log/secure;jsonFormat

日志样例:
Dec 20 20:45:29 iZqj0017jrl4ze990u9a8nZ filebeat: 2021-12-20T20:45:29.250+0800#011INFO#011[monitoring]#011log/log.go:145#011Non-zero metrics in the last 30s#011{"monitoring": {"metrics": {"beat":{"cpu":{"system":{"ticks":2076420,"time":{"ms":6}},"total":{"ticks":4129400,"time":{"ms":11},"value":4129400},"user":{"ticks":2052980,"time":{"ms":5}}},"handles":{"limit":{"hard":4096,"soft":1024},"open":12},"info":{"ephemeral_id":"712106ea-c635-4c7e-a39b-e39e1a50aaca","uptime":{"ms":13753620058}},"memstats":{"gc_next":22069600,"memory_alloc":16125216,"memory_total":206065956552},"runtime":{"goroutines":34}},"filebeat":{"events":{"added":2,"done":2},"harvester":{"open_files":1,"running":1}},"libbeat":{"config":{"module":{"running":0}},"output":{"events":{"acked":2,"batches":1,"total":2},"read":{"bytes":12},"write":{"bytes":494}},"pipeline":{"clients":2,"events":{"active":0,"published":2,"total":2},"queue":{"acked":2}}},"registrar":{"states":{"current":18,"update":2},"writes":{"success":1,"total":1}},"system":{"load":{"1":0,"15":0.05,"5":0.01,"norm":{"1":0,"15":0.0125,"5":0.0025}}}}}}
============================================================================================================
filebeat

filebeat.yml
filebeat.inputs:
- type: log
  enabled: true
  paths:
     - /var/log/history/*.*
  encoding: utf-8
  tail_files: true
  fields:
    app: linux-syslog
    type: linux-history-log
  fields_under_root: true

- type: log
  enabled: true
  paths:
     - /var/log/secure*
  include_lines: [".*Failed.*",".*Accepted.*"]
  encoding: utf-8
  tail_files: true
  fields:
    app: linux-syslog
    type: linux-secure-log
  fields_under_root: true

filebeat.config.modules:
  path: ${path.config}/modules.d/*.yml
  reload.enabled: false
setup.template.settings:
  index.number_of_shards: 1
setup.kibana:

output.logstash:
  hosts: ["10.160.58.101:15044"]
```



