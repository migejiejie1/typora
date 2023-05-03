**dingtalk使用自定义告警模板**

本文内容基于 [k8s部署prometheus + grafana](https://blog.csdn.net/miss1181248983/article/details/107673128)，只针对 dingtalk 部分进行修改。

```bash
vim dingtalk/config.yaml

apiVersion: v1
kind: ConfigMap
metadata:
  name: dingtalk-config
  namespace: monitoring
data:
  config.yml: |-
    templates:
      - /etc/prometheus-webhook-dingtalk/default.tmpl
    targets:
      webhook:
        url: https://oapi.dingtalk.com/robot/send?access_token=xxxxxxxxxxxx             #修改为钉钉机器人的webhook
#       message:
#         title: '{{ template "legacy.title" . }}'             #使用 Legacy（旧版） 模板
#         text: '{{ template "legacy.content" . }}'
        mention:
          all: true             #@所有人

  default.tmpl: |
    {{ define "__subject" }}[{{ .Status | toUpper }}{{ if eq .Status "firing" }}:{{ .Alerts.Firing | len }}{{ end }}] {{ .GroupLabels.SortedPairs.Values | join " " }} {{ if gt (len .CommonLabels) (len .GroupLabels) }}({{ with .CommonLabels.Remove .GroupLabels.Names }}{{ .Values | join " " }}{{ end }}){{ end }}{{ end }}
    {{ define "__alertmanagerURL" }}{{ .ExternalURL }}/d/9CWBz0bik/k8sji-qun-shou-ye?orgId=1 {{ end }}
    
    {{ define "__text_alert_list" }}{{ range . }}
    **Labels**
    {{ range .Labels.SortedPairs }}> - {{ .Name }}: {{ .Value | markdown | html }}
    {{ end }}
    **Annotations**
    {{ range .Annotations.SortedPairs }}> - {{ .Name }}: {{ .Value | markdown | html }}
    {{ end }}
    **Source:** [{{ .GeneratorURL }}]({{ .GeneratorURL }})
    {{ end }}{{ end }}
    
    {{ define "default.__text_alert_list" }}{{ range . }}
    ---
    **告警级别:** {{ .Labels.severity | upper }}
    
    **触发时间:** {{ dateInZone "2006.01.02 15:04:05" (.StartsAt) "Asia/Shanghai" }}
    
    **事件信息:**
    {{ range .Annotations.SortedPairs }}> - {{ .Name }}: {{ .Value | markdown | html }}    

    {{ end }}{{ end }}
    {{ end }}

    {{ define "default.__text_alertresolve_list" }}{{ range . }}
    ---
    **告警级别:** {{ .Labels.severity | upper }}
    
    **触发时间:** {{ dateInZone "2006.01.02 15:04:05" (.StartsAt) "Asia/Shanghai" }}
    
    **结束时间:** {{ dateInZone "2006.01.02 15:04:05" (.EndsAt) "Asia/Shanghai" }}
    
    **事件信息:**
    {{ range .Annotations.SortedPairs }}> - {{ .Name }}: {{ .Value | markdown | html }}    

    {{ end }}{{ end }}
    {{ end }}
    
    {{/* Default */}}
    {{ define "default.title" }}{{ template "__subject" . }}{{ end }}
    {{ define "default.content" }}#### \[{{ .Status | toUpper }}{{ if eq .Status "firing" }}:{{ .Alerts.Firing | len }}{{ end }}\] **[{{ index .GroupLabels "alertname" }}]({{ template "__alertmanagerURL" . }})**

    {{ if gt (len .Alerts.Firing) 0 -}} 
    ![警报 图标](http://wx4.sinaimg.cn/large/006APoFYly1g0ggpt1aq3j306o06o3yt.jpg)

    **=====侦测到故障=====**
    {{ template "default.__text_alert_list" .Alerts.Firing }} 
    {{- end }}
    
    {{ if gt (len .Alerts.Resolved) 0 -}}
    ![警报 图标](https://i01piccdn.sogoucdn.com/2dbf8a9d48e921e7)

    **=====故障已修复=====**
    {{ template "default.__text_alertresolve_list" .Alerts.Resolved }}
    {{- end }}
    {{- end }}
    
    {{/* Legacy */}}
    {{ define "legacy.title" }}{{ template "__subject" . }}{{ end }}
    {{ define "legacy.content" }}#### \[{{ .Status | toUpper }}{{ if eq .Status "firing" }}:{{ .Alerts.Firing | len }}{{ end }}\] **[{{ index .GroupLabels "alertname" }}]({{ template "__alertmanagerURL" . }})**
    {{ template "__text_alert_list" .Alerts.Firing }}
    {{- end }}
    
    {{/* Following names for compatibility */}}
    {{ define "ding.link.title" }}{{ template "default.title" . }}{{ end }}
    {{ define "ding.link.content" }}{{ template "default.content" . }}{{ end }}
```

```bash
vim dingtalk/dingtalk.yaml

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: dingtalk
  namespace: monitoring
spec:
  rules:
  - host: dingtalk.lzxlinux.cn
    http:
      paths:
      - path: /
        backend:
          serviceName: dingtalk
          servicePort: 8060

---
apiVersion: v1
kind: Service
metadata:
  name: dingtalk
  namespace: monitoring
  labels:
    app: dingtalk
  annotations:
    prometheus.io/scrape: 'false'
spec:
  selector:
    app: dingtalk
  ports:
  - name: dingtalk
    port: 8060
    protocol: TCP
    targetPort: 8060
  
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: dingtalk
  namespace: monitoring
spec:
  replicas: 1
  selector:
    matchLabels:
      app: dingtalk
  template:
    metadata:
      name: dingtalk
      labels:
        app: dingtalk
    spec:
      containers:
      - name: dingtalk
        image: timonwong/prometheus-webhook-dingtalk:latest
        imagePullPolicy: IfNotPresent
        args:
        - --web.listen-address=:8060
        - --config.file=/etc/prometheus-webhook-dingtalk/config.yml
        - --web.enable-ui             #启动ui访问，进行模板验证
        ports:
        - containerPort: 8060
        volumeMounts:
        - name: config
          mountPath: /etc/prometheus-webhook-dingtalk
      securityContext:
        runAsUser: 0
        fsGroup: 0
      volumes:
      - name: config
        configMap:
          name: dingtalk-config
```

```bash
kubectl apply -f dingtalk/
```

本地添加hosts，访问 `https://dingtalk.lzxlinux.cn/ui`，进行模板验证，

<img src="C:\Users\11650\AppData\Roaming\Typora\typora-user-images\image-20230328182055807.png" alt="image-20230328182055807" style="zoom:50%;" />

- 测试告警：

模拟CPU使用率为80%，

```bash
yum install -y stress-ng

stress-ng -c 0 -l 80                # -c 指定压力源进程的数量，以匹配在线CPU的数量，0表示加载每个cpu；-l 指定CPU使用率；Ctrl + C 退出
```

收到故障告警：

<img src="C:\Users\11650\AppData\Roaming\Typora\typora-user-images\image-20230328182134140.png" alt="image-20230328182134140" style="zoom:50%;" />

收到恢复告警：

<img src="C:\Users\11650\AppData\Roaming\Typora\typora-user-images\image-20230328182158824.png" alt="image-20230328182158824" style="zoom:50%;" />

需要注意的是，default.tmpl 中包含了 /d/9CWBz0bik/k8sji-qun-shou-ye?orgId=1，这正是 grafana 的 url。因为 alertmanager 的 --web.external-url 参数已经被更改为 grafana 地址 http://grafana.lzxlinux.cn，最终点击告警消息中的链接就能够跳转到 http://grafana.lzxlinux.cn/d/9CWBz0bik/k8sji-qun-shou-ye?orgId=1。


![image-20230328182220301](C:\Users\11650\AppData\Roaming\Typora\typora-user-images\image-20230328182220301.png)

default.tmpl 中包含的 url 请按自己的情况设置，dingtalk 使用自定义告警模板配置至此完成。具体还可参考：[prometheus-webhook-dingtalk](https://github.com/timonwong/prometheus-webhook-dingtalk/blob/master/docs/FAQ_zh.md)