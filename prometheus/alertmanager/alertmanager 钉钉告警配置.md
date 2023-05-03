**alertmanager 钉钉告警配置**

集成了这个项目，用于钉钉机器人推送告警信息
 [https://github.com/timonwong/prometheus-webhook-dingtalk](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Ftimonwong%2Fprometheus-webhook-dingtalk)

prometheus-webhook-dingtalk部署

1. 二进制部署

```shell
# 二进制包下载
https://github.com/timonwong/prometheus-webhook-dingtalk/releases
wget https://github.com/timonwong/prometheus-webhook-dingtalk/releases/download/v0.3.0/prometheus-webhook-dingtalk-0.3.0.linux-amd64.tar.gz

# 启动服务, 该版本为老版本, 新版本有config配置文件
./prometheus-webhook-dingtalk --ding.profile="webhook1=https://oapi.dingtalk.com/robot/send?access_token={替换成自己的dingding token}"


# 新版本
https://github.com/timonwong/prometheus-webhook-dingtalk/releases/tag/v2.1.0
wget https://objects.githubusercontent.com/github-production-release-asset-2e65be/107095747/f76c0d00-0a84-4e3b-ab2d-d794cc53aa8e?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20221125%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20221125T075612Z&X-Amz-Expires=300&X-Amz-Signature=2479e159746a47675b2568680694fb7918ddda3c32ccb5f35422f108b526f586&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=107095747&response-content-disposition=attachment%3B%20filename%3Dprometheus-webhook-dingtalk-2.1.0.linux-amd64.tar.gz&response-content-type=application%2Foctet-stream
```

```shell
# 配置文件
$ cat config.yml   
## Request timeout
timeout: 5s

## Uncomment following line in order to write template from scratch (be careful!)
#no_builtin_template: true

## Customizable templates path
#templates:
#  - contrib/templates/legacy/template.tmpl

## You can also override default template using `default_message`
## The following example to use the 'legacy' template from v0.3.0
#default_message:
#  title: '{{ template "legacy.title" . }}'
#  text: '{{ template "legacy.content" . }}'

## Targets, previously was known as "profiles"
targets:
  webhook1:   #替换成自己的dingding地址
    url: https://oapi.dingtalk.com/robot/send?access_token=5fc87906caf5986a7320194c53b6bbd13762c37a329ba28150e2e5833eea22b2
    # secret for signature
    secret: SEC66222263bc8d900bcdb4f83448699618f9cd38b3ea44486867e7f8ad5776fbb5

```

```shell
# 启动服务
nohup /app/prometheus-webhook-dingtalk/prometheus-webhook-dingtalk \
      --web.listen-address=":8060" \
      --web.enable-ui \
      --web.enable-lifecycle \
      --config.file=/app/prometheus-webhook-dingtalk/config.yml \
      --log.level=info \
      --log.format=logfmt \
      &>> /app/prometheus-webhook-dingtalk/logs/webhook-$(date +%F_%H%M).log &
```

2. docker部署

```shell
docker pull timonwong/prometheus-webhook-dingtalk

# 启动容器
docker run -d -p 8060:8060 --name webhook timonwong/prometheus-webhook --ding.profile="webhook1=https://oapi.dingtalk.com/robot/send?access_token={替换成自己的dingding token}
```

3. **alert配置**

```shell
cat alertmanager.yml

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
  - url: http://localhost:8060/dingtalk/webhook1/send   # prometheus-webhook-dingtalk 地址
    send_resolved: true
```

4. **邮箱告警配置**

```shell
global:
# 在没有报警的情况下声明为已解决的时间
  resolve_timeout: 2m
# 配置邮件发送信息
  smtp_smarthost: 'smtp.qiye.aliyun.com:465'
  smtp_from: 'your_email'
  smtp_auth_username: 'your_email'
  smtp_auth_password: 'email_passwd'
  smtp_hello: 'your_email'
  smtp_require_tls: false

  # 所有报警信息进入后的根路由，用来设置报警的分发策略
route:
# 这里的标签列表是接收到报警信息后的重新分组标签，例如，接收到的报警信息里面有许多具有 cluster=A 和 alertname=LatncyHigh 这样的标签的报警信息将会批量被聚合到一个分组里面
  group_by: ['alertname', 'cluster']
# 当一个新的报警分组被创建后，需要等待至少group_wait时间来初始化通知，这种方式可以确保您能有足够的时间为同一分组来获取多个警报，然后一起触发这个报警信息。
  group_wait: 30s

# 当第一个报警发送后，等待'group_interval'时间来发送新的一组报警信息。
  group_interval: 5m

 # 如果一个报警信息已经发送成功了，等待'repeat_interval'时间来重新发送他们
  repeat_interval: 5m

# 默认的receiver：如果一个报警没有被一个route匹配，则发送给默认的接收器
  receiver: default  # 优先使用default发送

# 上面所有的属性都由所有子路由继承，并且可以在每个子路由上进行覆盖。
  routes: #子路由，使用email发送
  - receiver: email
    match_re:
      serverity : email  # label 匹配email
    group_wait: 10s
receivers:
- name: 'default'
  webhook_configs:
  - url: http://localhost:8060/dingtalk/webhook1/send  
    send_resolved: true # 发送已解决通知

- name: 'email'
  email_configs:
  - to: 'email@qq.com'
    send_resolved: true
```

**参考博客**
[https://theo.im/blog/2017/10/16/release-prometheus-alertmanager-webhook-for-dingtalk/](https://links.jianshu.com/go?to=https%3A%2F%2Ftheo.im%2Fblog%2F2017%2F10%2F16%2Frelease-prometheus-alertmanager-webhook-for-dingtalk%2F)