```shell
input {
    file {
        path => ["/var/log/*.log", "/var/log/message"]
        type => "system"
        start_position => "beginning"
    }
}

input {
    stdin {
        add_field => {"key" => "value"}
        codec => "plain"
        tags => ["add"]
        type => "std"
    }
}

input {
  syslog {
    port => "514"
  }
}

input {
  tcp {
    port => "8514"
  }
}
filter {
  grok {
    match => ["message", "%{SYSLOGLINE}" ]
  }
  syslog_pri { }
}

input {
    tcp {
        port => 8888
        mode => "server"
        ssl_enable => false
    }
}

input | decode | filter | encode | output

log_format json '{"@timestamp":"$time_iso8601",'
               '"@version":"1",'
               '"host":"$server_addr",'
               '"client":"$remote_addr",'
               '"size":$body_bytes_sent,'
               '"responsetime":$request_time,'
               '"domain":"$host",'
               '"url":"$uri",'
               '"status":"$status"}';
access_log /var/log/nginx/access.log_json json;

input {
    file {
        path => "/var/log/nginx/access.log_json"
        codec => "json"
    }
}

input {
    stdin {
        codec => multiline {
            pattern => "^\["
            negate => true
            what => "previous"
        }
    }
}

Hostname "mige.host"
LoadPlugin interface
LoadPlugin cpu
LoadPlugin memory
LoadPlugin network
LoadPlugin df
LoadPlugin disk
<Plugin interface>
    Interface "ens32"
    IgnoreSelected false
</Plugin>
<Plugin network>
    # logstash 的 IP 地址和 collectd 的数据接收端口号>
    # 如果logstash和collectd在同一台主机上也可以用环回地址127.0.0.1
    <Server "10.0.0.1" "25826">
    </Server>
</Plugin>

input {
    udp {
        port => 25826
        buffer_size => 1452
        workers => 3          # Default is 2
        queue_size => 30000   # Default is 2000
        codec => collectd { }
        type => "collectd"
    }
}

input {stdin{}}
filter {
    grok {
        match => {
            "message" => "\s+(?<request_time>\d+(?:\.\d+)?)\s+"
        }
    }
}
output {stdout{codec => rubydebug}}


```

