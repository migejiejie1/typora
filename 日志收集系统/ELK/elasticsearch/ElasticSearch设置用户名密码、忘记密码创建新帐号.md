ElasticSearch设置用户名密码、忘记密码创建新帐号

es版本号:7.4.0

参考： http://www.likecs.com/show-99086.html

设置密码
1.需要在配置文件中开启x-pack验证, 修改config(一般是在/usr/share/elasticsearch)目录下面的elasticsearch.yml文件，在里面添加如下内容,并重启.

```
xpack.security.enabled: true
xpack.license.self_generated.type: basic
xpack.security.transport.ssl.enabled: true
```

2，执行设置用户名和密码的命令,这里需要为4个用户分别设置密码，elastic, kibana, logstash_system,beats_system

```
# bin/elasticsearch-setup-passwords interactive

Initiating the setup of passwords for reserved users elastic,kibana,logstash_system,beats_system.
You will be prompted to enter passwords as the process progresses.
Please confirm that you would like to continue [y/N]y
Enter password for [elastic]: 
passwords must be at least [6] characters long
Try again.
Enter password for [elastic]: 
Reenter password for [elastic]: 
Passwords do not match.
Try again.
Enter password for [elastic]: 
Reenter password for [elastic]: 
Enter password for [kibana]: 
Reenter password for [kibana]: 
Enter password for [logstash_system]: 
Reenter password for [logstash_system]: 
Enter password for [beats_system]: 
Reenter password for [beats_system]: 
Changed password for user [kibana]
Changed password for user [logstash_system]
Changed password for user [beats_system]
Changed password for user [elastic]
```

3，修改密码命令如下

```
curl -H "Content-Type:application/json" -XPOST -u elastic 'http://127.0.0.1:9200/_xpack/security/user/elastic/_password' -d '{ "password" : "123456" }'
```

4，忘记密码 
进入es的机器

```
docker exec -it elasticsearch /bin/bash
```

创建一个临时的超级用户 RyanMiao用这个用户去修改elastic用户的密码：

```
curl -XPUT -u ryan:ryan123 http://localhost:9200/_xpack/security/user/elastic/_password -H 
"Content-Type: application/json" -d '
{
  "password": "q5f2qNfUJQyvZPIz57MZ"
}'
```

