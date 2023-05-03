```shell
logstash 

logstash.conf
input {
  beats {
    port => "15044"
    codec => "json"
  }
}

filter {
    date {
        match => ["time", "yyyy-MM-dd HH:mm:ss"]
    }
}


filter {
    grok {
        match => {
            "msg" => "(?<result>\S+) (?<authtype>\S+) for (?<user>\S+) from (?<clientip>(?:\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3})?) port .*"
        }
        overwrite => ["msg"]
    }
}

output {
    stdout { codec => rubydebug }
    if [app] == "linux-syslog" {
        if [type] == "linux-history-log" {
           elasticsearch {
              hosts => ["http://10.160.58.102:19200","http://10.160.58.103:19200","http://10.160.58.104:19200"]
              user => "elastic"
              password => "Xtz#2021!"
              index => "linux-history-log-%{+YYYY.MM.dd}"
           }
        }
        else if [type] == "linux-secure-log" {
           elasticsearch {
              hosts => ["http://10.160.58.102:19200","http://10.160.58.103:19200","http://10.160.58.104:19200"]
              user => "elastic"
              password => "Xtz#2021!"
              index => "linux-secure-log-%{+YYYY.MM.dd}"
           }
        }
    }
}

logstash.yml
pipeline.ordered: auto
http.port: 19600-19700
xpack.monitoring.enabled: true
xpack.monitoring.elasticsearch.username: elastic
xpack.monitoring.elasticsearch.password: Xtz#2021!
xpack.monitoring.elasticsearch.hosts: ["10.160.58.102:19200", "10.160.58.103:19200", "10.160.58.104:19200"]

jvm.options
-Xms16g
-Xmx16g

startup.options
LS_HOME=/usr/share/logstash
LS_SETTINGS_DIR=/etc/logstash
LS_OPTS="--path.settings ${LS_SETTINGS_DIR}"
LS_JAVA_OPTS=""
LS_PIDFILE=/var/run/logstash.pid
LS_USER=logstash
LS_GROUP=logstash
LS_GC_LOG_FILE=/var/log/logstash/gc.log
LS_OPEN_FILES=16384
LS_NICE=19
SERVICE_NAME="logstash"
SERVICE_DESCRIPTION="logstash"
```

