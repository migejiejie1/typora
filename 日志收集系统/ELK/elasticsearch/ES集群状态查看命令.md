ES集群状态查看命令
Elasticsearch中信息很多，同时ES也有很多信息查看命令，可以帮助开发者快速查询Elasticsearch的相关信息。

```shell
1. _cat
# curl localhost:9200/_cat
=^.^=
/_cat/allocation
/_cat/shards
/_cat/shards/{index}
/_cat/master
/_cat/nodes
/_cat/indices
/_cat/indices/{index}
/_cat/segments
/_cat/segments/{index}
/_cat/count
/_cat/count/{index}
/_cat/recovery
/_cat/recovery/{index}
/_cat/health
/_cat/pending_tasks
/_cat/aliases
/_cat/aliases/{alias}
/_cat/thread_pool
/_cat/plugins
/_cat/fielddata
/_cat/fielddata/{fields}
/_cat/nodeattrs
/_cat/repositories
/_cat/snapshots/{repository}

2. verbose
每个命令都支持使用?v参数，来显示详细的信息：
# curl 172.16.221.104:9400/_cat/master?v
id                    		   host             ip               node
xO8UJK_dQTKvv1wgpYQIKg          *.*.221.12       *.*.221.12      node1

3. help
每个命令都支持使用help参数，来输出可以显示的列：
# curl 172.16.221.104:9400/_cat/master?help
id        |    | node id    
host    | h | host name  
ip        |    | ip address 
node  | n | node name

4. headers
通过h参数，可以指定输出的字段：
# curl 172.16.221.104:9400/_cat/master?v
id                                                  host               ip             node
xO8UJK_dQTKvv1wgpYQIKg *.*.221.12     *.*.221.12     node1

# curl 172.16.221.104:9400/_cat/master?h=ip,node
*.*.221.12      node1

5. 数字类型的格式化
很多的命令都支持返回可读性的大小数字，比如使用mb或者kb来表示。
# curl 172.16.221.104:9400/_cat/indices?v
health status   index                  uuid                                             pri rep docs.count docs.deleted store.size  pri.store.size
yellow open   .kibana                QTYWgFweRJm2RohdZQKLRw   1   1         41            1                  140.3kb        140.3kb
......
......
......

```

