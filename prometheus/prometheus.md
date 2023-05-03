```shell
# my global config 我的全局配置
global:
  scrape_interval: 15s # 将 scrape interval 间隔设置为每15秒一次。默认为每1分钟一次。
  evaluation_interval: 15s # 每15秒评估一次规则。默认为每1分钟一次。
  # scrape_timeout 被设置为全局默认值(10秒)。

# 报警管理程序配置
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          # - alertmanager:9093

# 只加载一次规则，并根据全局'evaluation_interval'周期性地对它们进行评估。rule 规则
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# 一种scrape配置，只包含一个要scrape的端点:  
# Here it's Prometheus itself.
scrape_configs:
  # 作业名称作为标签' job=<job_name> '添加到从该配置中抓取的任何时间序列。  
  - job_name: "prometheus"

    #  metrics 路径默认为 '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ["localhost:9090"]

  - job_name: "node2"
    static_configs:
      - targets: ["192.168.1.12:9100"]
  - job_name: "node3"
    static_configs:
      - targets: ["192.168.1.13:9100"]	  
	  
	  
http_request_status{code='200',content_path='/api/path', environment='produment'} => [value1@timestamp1,value2@timestamp2...]

http_request_status{code='200',content_path='/api/path2', environment='produment'} => [value1@timestamp1,value2@timestamp2...]	  

```

