```shell
rpm方式安装elasticsearch修改启动内存方法

为了某项目的迁移，近期让来的实习生小伙伴单独搭建了一套Elasticsearch(以下简称ES)环境，搭建完毕是好的，但是跑了几天就出现9200端口页面无法打开的情况！导致无法查看到索引监控等页面。

分析了下问题，发现后台爆出的日志出现了OOM的问题。于是按照流程去找到了下问题。

1.查看ES服务启动内容
ps -aux |grep elasticsearch

发现启动内存是256M-1G，竟然如此之小！！！那不OOM才怪！！！！

于是准备修改启动内存。

2.找到修改启动内存文件
由于小伙伴是采用rpm安装方式安装的，默认在ES的安装路径下是找不到修改内存文件的。

3.查找内存修改文件位置
cd /etc/sysconfig/

4.修改配置文件内容
vi elasticsearch

我们看到官方文件中给予我们提示的是：如果我们不打开此处的控制，那么ES默认会设置启动内存为256M-1G的内存

# Heap size defaults to 256m min, 1g max

# Set ES_HEAP_SIZE to 50% of available RAM, but no more than 31g

#ES_HEAP_SIZE=2g
ES_HEAP_SIZE=20g


5.修改启动内存为你需要的大小
这里修改成了2G，记得修改完毕后要把注释放开，去掉前面的！！！！#

6.再次重启ES，观察
执行如下命令，查看是否已修改成功，若出现启动内存显示为2G，证明修改成功！！

ps -aux |grep elasticsearch

修改完毕内存后，再次观察，未在出现OOM问题。大工告成！！！！

===========================================================================

elasticsearch设置启动内存三种方式
已有 1007 次阅读2019-1-4 10:47 |个人分类:ElasticSearch|系统分类:大数据| 设置启动内存

以下三种方式中前两种在我使用过程中未生效。
1.修改es的家目录中的启动文件：vim /elasticsearch-2.3.4/bin/elasticsearch
添加：
export ES_HEAP_SIZE=10g

2.修改es配置文件： vim elasticsearch-2.3.4/config/elasticsearch.yml
添加：
##分配给es的最小内存 让min == max 建议怎么做，让gc跑起来
set.default.ES_MIN_MEM=4620
##分配给es的最大内存 
set.default.ES_MAX_MEM=4620

3.启动时添加限制参数
ES_JAVA_OPTS="-Xms2g -Xmx2g"  ./bin/elasticsearch

同时设置对交换空间的使用
#尽量不用交换空间
#设置为1 而不是0 主要是可能出现内存满后会乱杀程序
vim /etc/sysctl.conf
添加： vm.swappiness = 1
```

