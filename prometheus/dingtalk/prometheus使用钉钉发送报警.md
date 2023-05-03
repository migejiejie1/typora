**prometheus使用钉钉发送报警**

由于公司oa系统更换成钉钉，监控报警一直在使用mail来收发，因为mail不是很及时导致有时可能会错过一些报警，于是决定将通知切换到钉钉上。

### **安装前提**:

 安装好 Prometheus  Alertmanager 服务端

### **获取钉钉webhook url**

创建群

进入设置，找到智能群助手

添加机器人，选择自定义

 安全设置，我这里选择ip，即来自此ip的请求，钉钉才会接受。可以理解为ip白名单

 添加之后便会看到机器人的webhook地址，复制下来，下面将用到。

### 安装prometheus-webhook-dingtalk

项目地址：https://github.com/timonwong/prometheus-webhook-dingtalk/releases

我这里采用的是最新的二进制安装包，[prometheus-webhook-dingtalk-1.4.0.linux-amd64.tar.gz](https://github.com/timonwong/prometheus-webhook-dingtalk/releases/download/v1.4.0/prometheus-webhook-dingtalk-1.4.0.linux-amd64.tar.gz)

 解压后重命名并计入目录

mv prometheus-webhook-dingtalk-1.4.0.linux-amd64/ prometheus-webhook-dingtalk

cd prometheus-webhook-dingtalk/

复制配置文件模板，prometheus-webhook-dingtalk使用此配置文件

cp config.example.yml config.yml

修改后config.yml内容如下:

```
## Request timeout
# timeout: 5s

## Customizable templates path
# templates:
#   - contrib/templates/legacy/template.tmpl

## You can also override default template using `default_message`
## The following example to use the 'legacy' template from v0.3.0
# default_message:
#   title: '{{ template "legacy.title" . }}'
#   text: '{{ template "legacy.content" . }}'

## Targets, previously was known as "profiles"
targets:
  webhook2:
    url: https://oapi.dingtalk.com/robot/send?access_token=29144b8baa8b2ffb03f8196f39c53a81eecafc67635b14af28762be82df0843b
    message:
      # Use legacy template
      title: '{{ template "ding.link.title" . }}'
      text: '{{ template "ding.link.content" . }}'
```

 此时使用默认的模板，contrib/templates/legacy/template.tmpl，先来测试下效果，待成功后再自定义内容模板。

配置服务启动脚本Centos7

```
# vi /usr/lib/systemd/system/prometheus-webhook-dingtalk.service 
[Unit]
Description='start prometheus-webhook-dingtalk service'
Documentation='https://github.com/timonwong/prometheus-webhook-dingtalk'
After=network.target

[Service]
Type=simple
User=root
PIDFile=/var/run/prometheus-webhook-dingtalk.pid
ExecStart=/usr/local/prometheus-webhook-dingtalk/prometheus-webhook-dingtalk \
      --web.listen-address=:8060 \
          --web.enable-lifecycle \
          --web.enable-ui \
          --config.file=/usr/local/prometheus-webhook-dingtalk/config.yml
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

 启动prometheus-webhook-dingtalk

```
systemctl daemon-reload
systemctl start prometheus-webhook-dingtalk
```

查看prometheus-webhook-dingtalk的url地址，altermanager会将通知像这个地址发送

```
journalctl -u prometheus-webhook-dingtalk -f
```

 可以看到url  urls=http://localhost:8060/dingtalk/webhook2/send

### 配置altermanager

添加receivers: dingtalk

```
route:
  receiver: dingtalk
  group_by: ['alertname','cluster','service']
  group_wait: 20s
  group_interval: 20s
  repeat_interval: 10m
  routes:
  - match_re:
      alertname: .*Memory_Usages.*
    receiver: mem
    repeat_interval: 15m

receivers:
  - name: 'dingtalk'
    webhook_configs:
    - url: 'http://localhost:8060/dingtalk/webhook2/send'
      send_resolved: true

inhibit_rules:
- source_match:
    severity: 'Critical'
  target_match:
    severity: 'Warning'
  equal: ['alertname', 'service']
```

 重新加载altermanager配置

```
kill -1 `ps aux|grep alertmana|grep -v grep|awk '{print $2}'`
```

### 测试

将一台服务器node_exporter进程停掉，等待dingtalk通知

![image-20230328182536171](C:\Users\11650\AppData\Roaming\Typora\typora-user-images\image-20230328182536171.png)

 完成！

如果觉得通知内容不好看，可以自己修改template.yml模板，模板语法是 golang 的 [text/template](https://golang.org/pkg/text/template/), 需要一定的学习来掌握

访问 `http://localhost:8060/ui` 进行验证，如图所示

![image-20230328182555743](C:\Users\11650\AppData\Roaming\Typora\typora-user-images\image-20230328182555743.png)