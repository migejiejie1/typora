```shell
================================ logstash-4 =====================================
logstash.conf
input {
    kafka {
        bootstrap_servers => ["10.160.58.31:19092,10.160.58.32:19092,10.160.58.33:19092,10.160.58.34:19092,10.160.58.35:19092"]
        client_id => "consumer-1-ecloud-log-exception"
        group_id => "logstash-ecloud-log-exception"
        auto_offset_reset => "latest"
        consumer_threads => 5
        decorate_events => true
        topics => ["ecloud-log-exception"]
        add_field => {"indexName" => "ecloud-log-exception"}
        codec => json {
            charset => "UTF-8"
        }
    }
    kafka {
        bootstrap_servers => ["10.160.58.31:19092,10.160.58.32:19092,10.160.58.33:19092,10.160.58.34:19092,10.160.58.35:19092"]
        client_id => "consumer-1-ecloud-log-usual"
        group_id => "logstash-ecloud-log-usual"
        auto_offset_reset => "latest"
        consumer_threads => 5
        decorate_events => true
        topics => ["ecloud-log-usual"]
        add_field => {"indexName" => "ecloud-log-usual"}
        codec => json {
            charset => "UTF-8"
        }
    }
}

filter {
    date {
        match => ["createDate", "UNIX_MS"]
        target => "@timestamp"
        locale => "cn"
    }
}

output {
    elasticsearch {
        hosts => ["10.160.58.1:19200", "10.160.58.2:19200", "10.160.58.3:19200"]
        index => "%{indexName}"
        user => "elastic"
        password => "changeme#2021!"
    }
}

jvm.options
-Xms32g
-Xmx32g

logstash.yml
node.name: ecloud-other
pipeline.workers: 8
pipeline.batch.size: 10000
pipeline.batch.delay: 50
pipeline.unsafe_shutdown: false
pipeline.ordered: auto
http.host: 10.160.58.21
http.port: 29600-29700
xpack.monitoring.enabled: true
xpack.monitoring.elasticsearch.username: elastic
xpack.monitoring.elasticsearch.password: changeme#2021!
xpack.monitoring.elasticsearch.hosts: ["10.160.58.1:19200", "10.160.58.2:19200", "10.160.58.3:19200"]

================================ logstash-1 =====================================
logstash.conf
input {
    kafka {
        bootstrap_servers => ["10.160.58.31:19092,10.160.58.32:19092,10.160.58.33:19092,10.160.58.34:19092,10.160.58.35:19092"]
        client_id => "consumer-1-k8s-logs"
        group_id => "logstash-k8s-logs"
        auto_offset_reset => "latest"
        consumer_threads => 5
        decorate_events => true
        topics => ["k8s-logs"]
        codec => json {
            charset => "UTF-8"
        }
    }
}

filter {
    grok {
        match => {
            "message" => "%{DATA:localtime}\s\[(?<thread>[\s\S]*)\]\s\[(?<traceId>[\s\S]*)\] \[(?<class>[\s\S]*)\]*?(\s|\n)%{LOGLEVEL:level}*?:\s(?<logMsg>[\s\S]*)"
        }
    }
    date {
        match => ["localtime", "yyyy-MM-dd HH:mm:ss.SSS"]
        target => "@timestamp"
    }
}

output {
    elasticsearch {
        hosts => ["10.160.58.1:19200", "10.160.58.2:19200", "10.160.58.3:19200"]
        index => "%{[fields][type]}-%{+YYYYMMdd}"
        user => "elastic"
        password => "changeme#2021!"
    }
}

jvm.options
-Xms32g
-Xmx32g

logstash.yml
node.name: k8s-logs
pipeline.workers: 8
pipeline.batch.size: 10000
pipeline.batch.delay: 50
pipeline.unsafe_shutdown: false
pipeline.ordered: auto
http.host: 10.160.58.21
http.port: 19600-19700
xpack.monitoring.enabled: true
xpack.monitoring.elasticsearch.username: elastic
xpack.monitoring.elasticsearch.password: changeme#2021!
xpack.monitoring.elasticsearch.hosts: ["10.160.58.1:19200", "10.160.58.2:19200", "10.160.58.3:19200"]


logstash1.conf
# Sample Logstash configuration for creating a simple
# Beats -> Logstash -> Elasticsearch pipeline.

input {
  kafka{
        bootstrap_servers => ["10.160.58.31:19092,10.160.58.32:19092,10.160.58.33:19092,10.160.58.34:19092,10.160.58.35:19092"]
        client_id => "logstash_test1"
        group_id => "logstash_test1"
        auto_offset_reset => "latest"
        consumer_threads => 5
        decorate_events => true
        topics => ["test1"]
        type => "test1"
  }

  kafka{
        bootstrap_servers => ["10.160.58.31:19092,10.160.58.32:19092,10.160.58.33:19092,10.160.58.34:19092,10.160.58.35:19092"]
        client_id => "logstash_test2"
        group_id => "logstash_test2"
        auto_offset_reset => "latest"
        consumer_threads => 5
        decorate_events => true
        topics => ["test2"]
        type => "test2"
  }

  kafka{
        bootstrap_servers => ["10.160.58.31:19092,10.160.58.32:19092,10.160.58.33:19092,10.160.58.34:19092,10.160.58.35:19092"]
        client_id => "logstash_test3"
        group_id => "logstash_test3"
        auto_offset_reset => "latest"
        consumer_threads => 5
        decorate_events => true
        topics => ["test3"]
        type => "test3"
  }

}

output {
  if [type] == "test1" {
    elasticsearch {
      hosts => ["10.160.58.1:19200", "10.160.58.2:19200", "10.160.58.3:19200"]
      index => "test1-%{+YYYY.MM.dd}"
      user => "elastic"
      password => "changeme"
    }
  }

  if [type] == "test2" {
    elasticsearch {
      hosts => ["10.160.58.1:19200", "10.160.58.2:19200", "10.160.58.3:19200"]
      index => "test2-%{+YYYY.MM.dd}"
      user => "elastic"
      password => "changeme"
    }
  }

  if [type] == "test3" {
    elasticsearch {
      hosts => ["10.160.58.1:19200", "10.160.58.2:19200", "10.160.58.3:19200"]
      index => "test3-%{+YYYY.MM.dd}"
      user => "elastic"
      password => "changeme"
    }
  }
}

================================ logstash-7 =====================================
logstash.conf
input {
    kafka {
        bootstrap_servers => ["10.160.58.31:19092,10.160.58.32:19092,10.160.58.33:19092,10.160.58.34:19092,10.160.58.35:19092"]
        client_id => "uat-dashboard-log"
        group_id => "uat-logstash-log"
        auto_offset_reset => "latest"
        consumer_threads => 5
        decorate_events => true
        topics => ["k8s-logs-uat"]
        codec => json {
            charset => "UTF-8"
        }
    }
    kafka {
        bootstrap_servers => ["10.160.58.31:19092,10.160.58.32:19092,10.160.58.33:19092,10.160.58.34:19092,10.160.58.35:19092"]
        client_id => "uat-dashboard-log"
        group_id => "uat-logstash-log"
        auto_offset_reset => "latest"
        consumer_threads => 5
        decorate_events => true
        topics => ["k8s-logs-uat-extranet"]
        codec => json {
            charset => "UTF-8"
        }
    }
}

filter {
    grok {
        match => {
            #"message" => "^%{DATA:localtime}\s\[(?<thread>[\s\S]*)\]\s\[(?<traceId>[\s\S]*)\] \[(?<class>[\s\S]*)\]*?(\s|\n)%{LOGLEVEL:level}*?:\s(?<logMsg>[\s\S]*)\s%{NOTSPACE:method}\s%{NOTSPACE:requestURL}\s\[(?<rTime>[\s\S]*)\]\s%{NOTSPACE}$"
            #"message" => "^%{DATA:localtime}\s\[(?<thread>[\s\S]*)\]\s\[(?<traceId>[\s\S]*)\] \[(?<class>[\s\S]*)\]*?(\s|\n)%{LOGLEVEL:level}*?:\s(?<logMsg>[\s\S]*)\s%{NOTSPACE:method}\s%{NOTSPACE:requestURL}\s\[(?<rTime:rTime:float>[\s\S]*)\]\s%{NOTSPACE}$"
            "message" => "^%{DATA:localtime}\s*%{LOGLEVEL:level}\s\[%{NOTSPACE:service},%{NOTSPACE},%{WORD},%{WORD}\]\s%{INT}\s%{NOTSPACE}\s\[(?<thread>[\s\S]*)\]\s%{NOTSPACE:aspect}\s*:\s%{NOTSPACE}\s%{NOTSPACE:class}\s%{NOTSPACE:method}\s%{NOTSPACE:requestMethod}\s%{URIPATHPARAM:requestURL}\s\[(?<rTime:rTime:float>[\s\S]*)\]\s%{NOTSPACE}$"
            #"message" => "^%{DATA:localtime}\s*\[(?<thread>[\s\S]*)\]\s\[(?<traceId>[\s\S]*)\] \[(?<aspect>[\s\S]*)\]*?(\s|\n)%{LOGLEVEL:level}\s:\s%{NOTSPACE}:%{NOTSPACE:service}\s%{NOTSPACE:class}\s%{NOTSPACE:method}\s%{NOTSPACE:requestMethod}\s%{NOTSPACE:requestURL}\s\[(?<rTime:rTime:float>[\s\S]*)\]\s%{NOTSPACE}$"
            #"message" => "^%{DATA:localtime}\s\[(?<thread>[\s\S]*)\]\s\[(?<traceId>[\s\S]*)\] \[(?<class>[\s\S]*)\]*?(\s|\n)%{LOGLEVEL:level}*?:\s%{NOTSPACE}\s%{NOTSPACE:service}\s%{NOTSPACE:dbname}\s%{NOTSPACE:mapper}\s\[(?<rTime:rTime:float>[\s\S]*)\]\s%{NOTSPACE}\s%{GREEDYDATA:SQL}$"
        }
    }
    #mutate {
    #    convert => [ "rTime", "integer"]
    #}
    date {
        match => ["localtime", "yyyy-MM-dd HH:mm:ss.SSS"]
        target => "@timestamp"
    }
}

output {
    stdout {
        codec => rubydebug
    }
}


logstash.yml
node.name: uat
pipeline.workers: 8
pipeline.batch.size: 10000
pipeline.batch.delay: 50
pipeline.unsafe_shutdown: false
pipeline.ordered: auto
http.host: 10.160.58.21
http.port: 30600-30700
xpack.monitoring.enabled: true
xpack.monitoring.elasticsearch.username: elastic
xpack.monitoring.elasticsearch.password: changeme#2021!
xpack.monitoring.elasticsearch.hosts: ["10.160.58.1:19200", "10.160.58.2:19200", "10.160.58.3:19200"]

 pipelines.yml
- pipeline.id: "dashboard-1"
  path.config: "/data/logstash-7/pipeline/uat-dashboard-1.conf"

- pipeline.id: "dashboard-2"
  path.config: "/data/logstash-7/pipeline/uat-dashboard-2.conf"

- pipeline.id: "k8s-logs-uat"
  path.config: "/data/logstash-7/pipeline/k8s-logs-uat.conf"

- pipeline.id: "k8s-logs-uat-extranet"
  path.config: "/data/logstash-7/pipeline/k8s-logs-uat-extranet.conf"

- pipeline.id: "other-uat"
  path.config: "/data/logstash-7/pipeline/other-uat.conf"

- pipeline.id: "k8s-logs-pre"
  path.config: "/data/logstash-7/pipeline/k8s-logs-pre.conf"

- pipeline.id: "k8s-logs-pre-extranet"
  path.config: "/data/logstash-7/pipeline/k8s-logs-pre-extranet.conf"

- pipeline.id: "other-pre"
  path.config: "/data/logstash-7/pipeline/other-pre.conf"

jvm.options
-Xms32g
-Xmx32g

k8s-logs-pre.conf
input {
    kafka {
        bootstrap_servers => ["10.160.58.31:19092,10.160.58.32:19092,10.160.58.33:19092,10.160.58.34:19092,10.160.58.35:19092"]
        client_id => "pre-k8s-logs"
        group_id => "pre-logstash-k8s-logs"
        auto_offset_reset => "latest"
        consumer_threads => 1
        decorate_events => true
        topics => ["k8s-logs-pre"]
        codec => json {
            charset => "UTF-8"
        }
    }

}

filter {
    grok {
        match => {
            "message" => "%{DATA:localtime}\s\[(?<thread>[\s\S]*)\]\s\[(?<traceId>[\s\S]*)\] \[(?<class>[\s\S]*)\]*?(\s|\n)%{LOGLEVEL:level}*?:\s(?<logMsg>[\s\S]*)"
        }
    }
    date {
        match => ["localtime", "yyyy-MM-dd HH:mm:ss.SSS"]
        target => "@timestamp"
    }
}

output {
    elasticsearch {
        hosts => ["10.160.58.1:19200", "10.160.58.2:19200", "10.160.58.3:19200"]
        index => "pre-%{[fields][type]}-%{+YYYYMMdd}"
        user => "elastic"
        password => "changeme#2021!"
    }
}
```

