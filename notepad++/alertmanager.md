```shell
[root@ds-slave ~]# ifconfig ens32| awk 'NR==2 {print $2}'
192.168.126.91

[root@ds-slave ~]# wget https://github.com/prometheus/alertmanager/releases/download/v0.23.0/alertmanager-0.23.0.linux-amd64.tar.gz
[root@ds-slave ~]# ls
alertmanager-0.23.0.linux-amd64.tar.gz
[root@ds-slave ~]# tar xf alertmanager-0.23.0.linux-amd64.tar.gz -C /usr/local
[root@ds-slave ~]# mv /usr/local/alertmanager-0.23.0.linux-amd64/ /usr/local/alertmanager

# 查看版本等信息
[root@ds-slave ~]# /usr/local/alertmanager/alertmanager --version
alertmanager, version 0.23.0 (branch: HEAD, revision: 61046b17771a57cfd4c4a51be370ab930a4d7d54)
  build user:       root@e21a959be8d2
  build date:       20210825-10:48:55
  go version:       go1.16.7
  platform:         linux/amd64

[root@ds-slave ~]# vim /usr/local/alertmanager/alertmanager.yml
# global：全局配置，主要配置告警方式，如邮件、webhook等。
global:
  resolve_timeout: 5m	# 超时,默认5min
  smtp_smarthost: 'smtp.qq.com:465'	# 这里为 QQ 邮箱 SMTP 服务地址，官方地址为 smtp.qq.com 端口为 465 或 587，同时要设置开启 POP3/SMTP 服务。
  smtp_from: '916719080@qq.com'
  smtp_auth_username: '916719080@qq.com'
  smtp_auth_password: 'lojdeopbholobgah'	# 这里为第三方登录 QQ 邮箱的授权码，非 QQ 账户登录密码，否则会报错，获取方式在 QQ 邮箱服务端设置开启 POP3/SMTP 服务时会提示。
  smtp_require_tls: false	# 是否使用 tls，根据环境不同，来选择开启和关闭。如果提示报错 email.loginAuth failed: 530 Must issue a STARTTLS command first，那么就需要设置为 true。着重说明一下，如果开启了 tls，提示报错 starttls failed: x509: certificate signed by unknown authority，需要在 email_configs 下配置 insecure_skip_verify: true 来跳过 tls 验证。

templates:	#  # 模板
  - '/usr/local/alertmanager/alert.tmp'

# route：用来设置报警的分发策略。Prometheus的告警先是到达alertmanager的根路由(route)，alertmanager的根路由不能包含任何匹配项，因为根路由是所有告警的入口点。
# 另外，根路由需要配置一个接收器(receiver)，用来处理那些没有匹配到任何子路由的告警（如果没有配置子路由，则全部由根路由发送告警），即缺省
# 接收器。告警进入到根route后开始遍历子route节点，如果匹配到，则将告警发送到该子route定义的receiver中，然后就停止匹配了。因为在route中
# continue默认为false，如果continue为true，则告警会继续进行后续子route匹配。如果当前告警仍匹配不到任何的子route，则该告警将从其上一级(
# 匹配)route或者根route发出（按最后匹配到的规则发出邮件）。查看你的告警路由树，https://www.prometheus.io/webtools/alerting/routing-tree-editor/,
# 将alertmanager.yml配置文件复制到对话框，然后点击"Draw Routing Tree"
route:
  group_by: ['alertname']	# 用于分组聚合，对告警通知按标签(label)进行分组，将具有相同标签或相同告警名称(alertname)的告警通知聚合在一个组，然后作为一个通知发送。如果想完全禁用聚合，可以设置为group_by: [...]
  group_wait: 30s	# 当一个新的告警组被创建时，需要等待'group_wait'后才发送初始通知。这样可以确保在发送等待前能聚合更多具有相同标签的告警，最后合并为一个通知发送。
  group_interval: 2m	# 当第一次告警通知发出后，在新的评估周期内又收到了该分组最新的告警，则需等待'group_interval'时间后，开始发送为该组触发的新告警，可以简单理解为，group就相当于一个通道(channel)。
  repeat_interval: 10m	# 告警通知成功发送后，若问题一直未恢复，需再次重复发送的间隔。
  receiver: 'email'		#  配置告警消息接收者，与下面配置的对应。例如常用的 email、wechat、slack、webhook 等消息通知方式。
  routes:	# 子路由
  - receiver: 'wechat'
    match:	# 通过标签去匹配这次告警是否符合这个路由节点；也可以使用  match_re 进行正则匹配
      severity: Disaster	# 标签severity为Disaster时满足条件，使用wechat警报

receivers:	# 配置报警信息接收者信息。
- name: 'email'	# 警报接收者名称
  email_configs:
  - to: '{{ template "email.to"}}'  # 接收警报的email（这里是引用模板文件中定义的变量）
    html: '{{ template "email.to.html" .}}' # 发送邮件的内容（调用模板文件中的）
#    headers: { Subject: " {{ .CommonLabels.instance }} {{ .CommonAnnotations.summary }}" }	# 邮件标题，不设定使用默认的即可
    send_resolved: true		# 故障恢复后通知

- name: 'wechat'
  wechat_configs:
  - corp_id: wwd76d598b5fad5097		# 企业信息("我的企业"--->"CorpID"[在底部])
    to_user: '@all'		# 发送给企业微信用户的ID，这里是所有人
#   to_party: '' 接收部门ID
    agent_id: 1000004	# 企业微信("企业应用"-->"自定应用"[Prometheus]--> "AgentId") 
    api_secret: DY9IlG0Bdwawb_ku0NblxKFrrmMwbLIZ7YxMa5rCg8g		# 企业微信("企业应用"-->"自定应用"[Prometheus]--> "Secret") 
    message: '{{ template "email.to.html" .}}'	# 发送内容（调用模板）
    send_resolved: true	 	# 故障恢复后通知

inhibit_rules:		# 抑制规则配置，当存在与另一组匹配的警报（源）时，抑制规则将禁用与一组匹配的警报（目标）。
  - source_match:
      severity: 'critical'
    target_match:
      severity: 'warning'
    equal: ['alertname', 'dev', 'instance']
    

# 修改好配置文件后，可以使用amtool工具检查配置
[root@ds-slave ~]# /usr/local/alertmanager/amtool check-config /usr/local/alertmanager/alertmanager.yml
Checking '/usr/local/alertmanager/alertmanager.yml'  SUCCESS
Found:
 - global config
 - route
 - 1 inhibit rules
 - 2 receivers
 - 1 templates
  SUCCESS

[root@ds-slave ~]# vim /usr/local/alertmanager/alert.tmp
{{ define "email.from" }}916719080@qq.com{{ end }}
{{ define "email.to" }}916719080@qq.com{{ end }}
{{ define "email.to.html" }}
{{- if gt (len .Alerts.Firing) 0 -}}{{ range .Alerts }}
<h2>@告警通知</h2>
告警程序: prometheus_alert <br>
告警级别: {{ .Labels.severity }} 级 <br>
告警类型: {{ .Labels.alertname }} <br>
故障主机: {{ .Labels.instance }} <br>
告警主题: {{ .Annotations.summary }} <br>
告警详情: {{ .Annotations.description }} <br>
触发时间: {{ .StartsAt.Local.Format "2006-01-02 15:04:05" }} <br>
{{ end }}{{ end -}}
{{- if gt (len .Alerts.Resolved) 0 -}}{{ range .Alerts }}
<h2>@告警恢复</h2>
告警程序: prometheus_alert <br>
故障主机: {{ .Labels.instance }}<br>
故障主题: {{ .Annotations.summary }}<br>
告警详情: {{ .Annotations.description }}<br>
告警时间: {{ .StartsAt.Local.Format "2006-01-02 15:04:05" }}<br>
恢复时间: {{ .EndsAt.Local.Format "2006-01-02 15:04:05" }}<br>
{{ end }}{{ end -}}
{{- end }}

# 上边模板文件配置了 email.from、email.to、email.to.html 三种模板变量，可以在 alertmanager.yml 文件中直接配置引用。这里 email.to.html 就是要发送的邮件内容，支持 Html 和 Text 格式，这里为了显示好看，采用 Html 格式简单显示信息。下边 {{ range .Alerts }} 是个循环语法，用于循环获取匹配的 Alerts 的信息。


[root@ds-slave ~]# /usr/local/alertmanager/alertmanager --config.file /usr/local/alertmanager/alertmanager.yml
level=info ts=2021-10-01T14:14:55.695Z caller=main.go:225 msg="Starting Alertmanager" version="(version=0.23.0, branch=HEAD, revision=61046b17771a57cfd4c4a51be370ab930a4d7d54)"
level=info ts=2021-10-01T14:14:55.695Z caller=main.go:226 build_context="(go=go1.16.7, user=root@e21a959be8d2, date=20210825-10:48:55)"
level=info ts=2021-10-01T14:14:55.700Z caller=cluster.go:184 component=cluster msg="setting advertise address explicitly" addr=192.168.126.91 port=9094
level=info ts=2021-10-01T14:14:55.705Z caller=cluster.go:671 component=cluster msg="Waiting for gossip to settle..." interval=2s
level=info ts=2021-10-01T14:14:55.751Z caller=coordinator.go:113 component=configuration msg="Loading configuration file" file=/usr/local/alertmanager/alertmanager.yml
level=info ts=2021-10-01T14:14:55.752Z caller=coordinator.go:126 component=configuration msg="Completed loading of configuration file" file=/usr/local/alertmanager/alertmanager.yml
level=info ts=2021-10-01T14:14:55.754Z caller=main.go:518 msg=Listening address=:9093
level=info ts=2021-10-01T14:14:55.754Z caller=tls_config.go:191 msg="TLS is disabled." http2=false
level=info ts=2021-10-01T14:14:57.707Z caller=cluster.go:696 component=cluster msg="gossip not settled" polls=0 before=0 now=1 elapsed=2.001884344s
level=info ts=2021-10-01T14:15:05.714Z caller=cluster.go:688 component=cluster msg="gossip settled; proceeding" elapsed=10.008544279s

[root@ds-slave ~]# ss -aulntp | grep 9093
tcp    LISTEN     0      128      :::9093                 :::*                   users:(("alertmanager",pid=101921,fd=8))


```

```shell
[root@test ~]# vim /usr/local/prometheus/prometheus.yml
global:
  scrape_interval: 15s 
  evaluation_interval: 15s
# 关联Alertmanager服务
alerting:
  alertmanagers:
    - static_configs:
        - targets:
           - 192.168.126.91:9093	# 指定Alertmanager 的地址

rule_files:		# 指定报警规则文件
  - "/usr/local/prometheus/rules/*.yml"

scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]
  - job_name: "alertmanager_test"	# 同时添加对alertmanager的监控
    static_configs:
      - targets: ["192.168.126.91:9093"]
  - job_name: "test"
    static_configs:
      - targets: ["192.168.126.41:9100"]


# 编辑报警规则文件
[root@test ~]# mkdir /usr/local/prometheus/rules
[root@test ~]# vim /usr/local/prometheus/rules/node_alerts.yml
groups:
- name: 实例存活告警规则
  rules:
  - alert: 实例存活告警 	# 告警规则的名称（alertname）
    expr: up == 0 		# expr 是计算公式，up指标可以获取到当前所有运行的Exporter实例以及其状态，即告警阈值为up==0
    for: 30s	# for语句会使 Prometheus 服务等待指定的时间, 然后执行查询表达式。（for 表示告警持续的时长，若持续时长小于该时间就不发给alertmanager了，大于该时间再发。for的值不要小于prometheus中的scrape_interval，例如scrape_interval为30s，for为15s，如果触发告警规则，则再经过for时长后也一定会告警，这是因为最新的度量指标还没有拉取，在15s时仍会用原来值进行计算。另外，要注意的是只有在第一次触发告警时才会等待(for)时长。）
    labels:		# labels语句允许指定额外的标签列表，把它们附加在告警上。
      severity: Disaster
    annotations:		# annotations语句指定了另一组标签，它们不被当做告警实例的身份标识，它们经常用于存储一些额外的信息，用于报警信息的展示之类的。
      summary: "节点失联"
      description: "节点断联已超过1分钟！"
- name: 内存告警规则
  rules:
  - alert: "内存使用率告警"
    expr: (node_memory_MemTotal_bytes - (node_memory_MemFree_bytes+node_memory_Buffers_bytes+node_memory_Cached_bytes )) / node_memory_MemTotal_bytes * 100 > 75	# 告警阈值为当内存使用率大于75%
    for: 30s
    labels:
      severity: warning
    annotations:
      summary: "服务器内存报警"
      description: "内存资源利用率大于75%！(当前值: {{ $value }}%)"

- name: 磁盘报警规则
  rules:
  - alert: 磁盘使用率告警
    expr: (node_filesystem_size_bytes - node_filesystem_avail_bytes) / node_filesystem_size_bytes * 100 > 80	# 告警阈值为某个挂载点使用大于80%
    for: 1m
    labels:
      severity: warning
    annotations:
      summary: "服务器 磁盘报警"
      description: "服务器磁盘设备使用超过80%！(挂载点: {{ $labels.mountpoint }} 当前值: {{ $value }}%)"

# 检查配置文件
[root@test ~]# /usr/local/prometheus/promtool check config /usr/local/prometheus/prometheus.yml 
Checking /usr/local/prometheus/prometheus.yml
  SUCCESS: 1 rule files found

Checking /usr/local/prometheus/rules/node_alerts.yml
  SUCCESS: 3 rules found

# 启动
[root@test ~]# nohup /usr/local/prometheus/prometheus --config.file=/usr/local/prometheus/prometheus.yml &
[1] 14316
[root@test ~]# nohup: ignoring input and appending output to ‘nohup.out’

```

```shell
ecloud.prometheus.cpms.tech 

10.160.47.104:80 -> 10.160.47.50:80 -> 10.160.58.128:80


[root@iZqj0017jrl4zeu4rm66vbZ ~]# kubectl get pods alertmanager-main-0 -n monitoring -o yaml 
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2022-03-16T02:22:41Z"
  generateName: alertmanager-main-
  labels:
    alertmanager: main
    app: alertmanager
    controller-revision-hash: alertmanager-main-6f76dbd48c
    statefulset.kubernetes.io/pod-name: alertmanager-main-0
  name: alertmanager-main-0
  namespace: monitoring
  ownerReferences:
  - apiVersion: apps/v1
    blockOwnerDeletion: true
    controller: true
    kind: StatefulSet
    name: alertmanager-main
    uid: 4116d525-a1e0-11ec-8401-00163e0103fb
  resourceVersion: "306084284"
  selfLink: /api/v1/namespaces/monitoring/pods/alertmanager-main-0
  uid: f501aa05-a4cf-11ec-b9bf-00163e010a9c
spec:
  containers:
  - args:
    - --config.file=/etc/alertmanager/config/alertmanager.yaml
    - --cluster.listen-address=[$(POD_IP)]:9094
    - --storage.path=/alertmanager
    - --data.retention=120h
    - --web.listen-address=:9093
    - --web.route-prefix=/
    - --cluster.peer=alertmanager-main-0.alertmanager-operated.monitoring.svc:9094
    env:
    - name: POD_IP
      valueFrom:
        fieldRef:
          apiVersion: v1
          fieldPath: status.podIP
    image: 10.160.1.151:7709/quay.io/prometheus/alertmanager:v0.18.0
    imagePullPolicy: IfNotPresent
    livenessProbe:
      failureThreshold: 10
      httpGet:
        path: /-/healthy
        port: web
        scheme: HTTP
      periodSeconds: 10
      successThreshold: 1
      timeoutSeconds: 3
    name: alertmanager
    ports:
    - containerPort: 9093
      name: web
      protocol: TCP
    - containerPort: 9094
      name: mesh-tcp
      protocol: TCP
    - containerPort: 9094
      name: mesh-udp
      protocol: UDP
    readinessProbe:
      failureThreshold: 10
      httpGet:
        path: /-/ready
        port: web
        scheme: HTTP
      initialDelaySeconds: 3
      periodSeconds: 5
      successThreshold: 1
      timeoutSeconds: 3
    resources:
      requests:
        memory: 200Mi
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: FallbackToLogsOnError
    volumeMounts:
    - mountPath: /etc/alertmanager/config
      name: config-volume
    - mountPath: /alertmanager
      name: alertmanager-main-db
      subPath: alertmanager-db
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: alertmanager-main-token-n67th
      readOnly: true
  - args:
    - -webhook-url=http://localhost:9093/-/reload
    - -volume-dir=/etc/alertmanager/config
    image: 10.160.1.151:7709/quay.io/coreos/configmap-reload:v0.0.1
    imagePullPolicy: IfNotPresent
    name: config-reloader
    resources:
      limits:
        cpu: 100m
        memory: 25Mi
      requests:
        cpu: 100m
        memory: 25Mi
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: FallbackToLogsOnError
    volumeMounts:
    - mountPath: /etc/alertmanager/config
      name: config-volume
      readOnly: true
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: alertmanager-main-token-n67th
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  hostname: alertmanager-main-0
  nodeName: cn-beijing-gzj1-d01.i-qj0017jrl4zew3sq8614
  nodeSelector:
    app: monitoring
  priority: 0
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext:
    fsGroup: 2000
    runAsNonRoot: true
    runAsUser: 1000
  serviceAccount: alertmanager-main
  serviceAccountName: alertmanager-main
  subdomain: alertmanager-operated
  terminationGracePeriodSeconds: 120
  tolerations:
  - effect: NoExecute
    key: node.kubernetes.io/not-ready
    operator: Exists
    tolerationSeconds: 300
  - effect: NoExecute
    key: node.kubernetes.io/unreachable
    operator: Exists
    tolerationSeconds: 300
  volumes:
  - name: alertmanager-main-db
    persistentVolumeClaim:
      claimName: alertmanager-main-db-alertmanager-main-0
  - name: config-volume
    secret:
      defaultMode: 420
      secretName: alertmanager-main
  - name: alertmanager-main-token-n67th
    secret:
      defaultMode: 420
      secretName: alertmanager-main-token-n67th
status:
  conditions:
  - lastProbeTime: null
    lastTransitionTime: "2022-03-16T02:22:41Z"
    status: "True"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2022-03-16T02:22:51Z"
    status: "True"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2022-03-16T02:22:51Z"
    status: "True"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2022-03-16T02:22:41Z"
    status: "True"
    type: PodScheduled
  containerStatuses:
  - containerID: docker://eaab98f0f4e627bd27733560f28f64e0687db8e634e5a0c93bb89c3c3ecae047
    image: 10.160.1.151:7709/quay.io/prometheus/alertmanager:v0.18.0
    imageID: docker-pullable://10.160.1.151:7709/quay.io/prometheus/alertmanager@sha256:3c0f0ea52a662342282fc7236ce058ab47a6259ed146cfc6bbdb122ba244c160
    lastState: {}
    name: alertmanager
    ready: true
    restartCount: 0
    state:
      running:
        startedAt: "2022-03-16T02:22:43Z"
  - containerID: docker://ca10e00cc01e0dee00ded9cac100cae1a3ef1c1dd46ad78a027f4f300abda730
    image: 10.160.1.151:7709/quay.io/coreos/configmap-reload:v0.0.1
    imageID: docker-pullable://10.160.1.151:7709/quay.io/coreos/configmap-reload@sha256:50c53db55ece9a6e1a7274e497f308bcc24164bdb4c0885524037c1b8e4e758d
    lastState: {}
    name: config-reloader
    ready: true
    restartCount: 0
    state:
      running:
        startedAt: "2022-03-16T02:22:44Z"
  hostIP: 10.160.59.70
  phase: Running
  podIP: 10.162.212.176
  qosClass: Burstable
  startTime: "2022-03-16T02:22:41Z"

```

```shell
/etc/prometheus $ cat prometheus.yml 
# my global config
global:
  scrape_interval:     15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
  - static_configs:
    - targets:
      # - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: 'prometheus'

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
    - targets: ['localhost:9090']


alerting:                                                           
  alert_relabel_configs:                                 
  - action: labeldrop                                    
    regex: prometheus_replica                                           
  alertmanagers:                                         
  - path_prefix: /                                       
    scheme: http                                         
    kubernetes_sd_configs:                               
    - role: endpoints                                    
      namespaces:                                        
        names:                            
        - monitoring                                
    relabel_configs:                                
    - action: keep                                                      
      source_labels:                                
      - __meta_kubernetes_service_name              
      regex: alertmanager-main               
    - action: keep                           
      source_labels:                                                    
      - __meta_kubernetes_endpoint_port_name               
      regex: web           
	  
rule_files:                                                
- /etc/prometheus/rules/prometheus-k8s-rulefiles-0/*.yaml  
	  
```

```shell
/etc/alertmanager/config $ cat alertmanager.yaml 
global:
  resolve_timeout: 5m
route:
  receiver: webhook
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 5m
  group_by: [alertname]
  routes:
  - receiver: webhook
    group_wait: 10s
receivers:
- name: webhook
  webhook_configs:
  - url: http://10.160.231.13:8060/dingtalk/ecloud/send
    send_resolved: true



/etc/alertmanager $ cat alertmanager.yml 
global:
  resolve_timeout: 5m

route:
  group_by: ['alertname']
  group_wait: 10s
  group_interval: 10s
  repeat_interval: 1h
  receiver: 'web.hook'
receivers:
- name: 'web.hook'
  webhook_configs:
  - url: 'http://127.0.0.1:5001/'
inhibit_rules:
  - source_match:
      severity: 'critical'
    target_match:
      severity: 'warning'
    equal: ['alertname', 'dev', 'instance']

```

