```shell
filebeat.yml
#=========================== Filebeat prospectors =============================
filebeat.inputs:
- type: log
  enabled: true
  paths:
    - "/app/logs/application/*/*.log"
  fields:
    type: ${APP_NAME}
  multiline.pattern: '^\d{4}-\d{1,2}-\d{1,2}'
  multiline.negate: true
  multiline.match: "after"
  tail_files: true
  close_removed: true
  scan_frequency: 1m
#================================ kafka =====================================
output.kafka:
  hosts: ["10.160.58.31:19092","10.160.58.32:19092","10.160.58.33:19092","10.160.58.34:19092","10.160.58.35:19092"]
  topic: "k8s-logs-sit"
  partition.round_robin:
  reachable_only: false
  required_acks: 1
  compression: gzip
  max_message_bytes: 200000
max_procs: 1

手动分割线

#================================ kafka =====================================
output.kafka:
  hosts: ["10.160.58.31:19092","10.160.58.32:19092","10.160.58.33:19092","10.160.58.34:19092","10.160.58.35:19092"]
  topic: "k8s-logs-pre"
  partition.round_robin:
  reachable_only: false
  required_acks: 1
  compression: gzip
  max_message_bytes: 200000
max_procs: 1
```

