```shell
重要的系统配置编辑
理想情况下，Elasticsearch应该在服务器上单独运行，并使用所有可用的资源。为此，您需要配置操作系统，以允许运行 Elasticsearch 的用户访问比默认允许的资源更多的资源。
https://www.elastic.co/guide/en/elasticsearch/reference/current/system-config.html

1. 临时目录设置
默认情况下，Elasticsearch 使用启动脚本在系统临时目录正下方创建的私有临时目录。 
请考虑为 Elasticsearch 创建一个专用的临时目录，该目录不在将从中清除旧文件和目录的路径下。此目录应设置权限，以便只有运行 Elasticsearch 的用户才能访问它。然后，在启动 Elasticsearch 之前，将环境变量设置为指向此目录。
$ES_TMPDIR

ES_TMPDIR="/usr/local/elasticsearch-7.16.2/tmp/"
PATH=$ES_TMPDIR:$PATH
export ES_TMPDIR PATH

2. ulimit编辑
在 Linux 系统上，可用于临时更改资源限制。通常需要设置限制，就像切换到将运行 Elasticsearch 的用户之前一样。
例如，若要将打开的文件句柄数 （） 设置为 65536，可以执行以下操作：
sudo su  
ulimit -n 65535 
su elasticsearch 
您可以使用 ulimit -a 查阅 所有当前应用的限制。

/etc/security/limits.conf编辑
在 Linux 系统上，可以通过编辑文件为特定用户设置持久性限制。若要将用户打开的最大文件数设置为 65535，请将以下行添加到文件中：
/etc/security/limits.conf
elasticsearch  -  nofile  65535
elasticsearch   soft    nofile  65535 # soft表示为超过这个值就会有warnning
elasticsearch   hadr    nofile  100000  # hard则表示不能超过这个值

3. 禁用交换编辑
大多数操作系统尝试将尽可能多的内存用于文件系统缓存，并急切地交换掉未使用的应用程序内存。这可能导致 JVM 堆的某些部分甚至其可执行页被交换到磁盘。
交换对性能和节点稳定性非常不利，应不惜一切代价避免。它可能导致垃圾回收持续几分钟而不是几毫秒，并可能导致节点响应缓慢，甚至与群集断开连接。在弹性分布式系统中，让操作系统终止节点会更有效。
有三种方法可以禁用交换。首选选项是完全禁用交换。如果这不是一个选项，则是否希望最小化交换性还是内存锁定取决于您的环境。

3.1 禁用所有交换文件
sudo swapoff -a
这不需要重新启动 Elasticsearch。
要永久禁用它，您需要编辑文件并注释掉包含单词的任何行。
/etc/fstab swap

3.2 配置swappiness
Linux 系统上可用的另一个选项是确保将 sysctl 值设置为 1。这减少了内核的交换倾向，并且在正常情况下不应导致交换，同时仍然允许整个系统在紧急情况下交换。
vm.swappiness 1

3.3 使bootstrap.memory_lock
另一种选择是在Linux /Unix系统上使用mlockall，或在Windows上使用VirtualLock，尝试将进程地址空间锁定到RAM中，以防止任何Elasticsearch堆内存被交换出去。
某些平台在使用内存锁时仍会交换堆外内存。要防止堆外内存交换，请改为禁用所有交换文件。
要启用内存锁定，请在 elasticsearch.yml 中设置为 
bootstrap.memory_lock: true

启动 Elasticsearch 后，您可以通过检查此请求的输出中的 值来查看此设置是否已成功应用：mlockall
GET _nodes?filter_path=**.mlockall
如果看到该值为 ，则表示请求已失败。您还将在日志中看到一行包含更多信息的单词。
mlockall false mlockall Unable to lock JVM Memory

在Linux/Unix系统上，最可能的原因是运行Elasticsearch的用户没有锁定内存的权限。这可以按如下方式授予：
在启动 Elasticsearch 之前，将ulimit -l unlimited设置为 root。或者，设置为 ：memlock unlimited /etc/security/limits.conf
# allow user 'elasticsearch' mlockall
elasticsearch soft memlock unlimited
elasticsearch hard memlock unlimited

可能失败的另一个可能原因是JNA 临时目录（通常是/tmp的子目录）是使用noexec选项挂载的。这可以通过使用环境变量为 JNA 指定一个新的临时目录来解决：mlockallES_JAVA_OPTS

export ES_JAVA_OPTS="$ES_JAVA_OPTS -Djna.tmpdir=<path>"
./bin/elasticsearch
或在 jvm.options 配置文件中设置此 JVM 标志。

4. 文件描述符
Elasticsearch 使用大量的文件描述符或文件句柄。文件描述符用完可能是灾难性的，并且很可能导致数据丢失。确保将运行 Elasticsearch 的用户打开的文件描述符数量限制提高到 65536 或更高。
请在启动 Elasticsearch 之前将ulimit -n 65535设置为 root，或设置为
/etc/security/limits.conf 
* nofile 65535

5. 虚拟内存
Elasticsearch 默认使用mmapfs目录来存储其索引。mmap 计数的默认操作系统限制可能太低，这可能会导致内存不足异常。
查看当前值
sysctl vm.max_map_count
在 Linux 上，您可以通过运行以下命令来增加限制：root
sysctl -w vm.max_map_count=262144
要永久设置此值，请更新 中的设置。要在重新启动后进行验证，请运行 。
vm.max_map_count=262144 /etc/sysctl.conf

sysctl vm.max_map_count

6. 线程数
Elasticsearch 使用许多线程池进行不同类型的操作。重要的是，它能够在需要时创建新线程。确保 Elasticsearch 用户可以创建的线程数至少为 4096。
这可以通过在启动 Elasticsearch 之前将ulimit -u 4096设置为 root 来完成，或者通过在
/etc/security/limits.conf 中设置为  * nproc 4096
elasticsearch   soft    nproc   4096
elasticsearch   hard    nproc   4096
作为服务运行时，软件包分发将自动配置 Elasticsearch 进程的线程数。无需其他配置。 systemd

7. DNS 缓存设置
Elasticsearch 在安全管理器就位的情况下运行。使用安全管理器后，JVM 默认无限期地缓存正主机名解析，并默认缓存负主机名解析十秒钟。Elasticsearch 使用默认值覆盖此行为，以将正查找缓存 60 秒，并将负查找缓存 10 秒。这些值应适用于大多数环境，包括 DNS 解析随时间变化的环境。如果没有，您可以编辑值和JVM 选项。请注意，Java 安全策略中的值networkaddress.cache.ttl=<timeout>和networkaddress.cache.negative.ttl=<timeout>将被 Elasticsearch 忽略，除非您删除 和 的设置。
.es.networkaddress.cache.ttles
.networkaddress.cache.negative.ttl
es.networkaddress.cache.ttl
es.networkaddress.cache.negative.ttl

8. JNA 临时目录未装入 noexec

Elasticsearch使用Java Native Access（JNA）库和另一个名为的库来执行一些依赖于平台的原生代码。在 Linux 上，支持这些库的本机代码在运行时被提取到一个临时目录中，然后映射到 Elasticsearch 地址空间中的可执行页面。这要求基础文件不在使用该选项挂载的文件系统上。libffi noexec

默认情况下，Elasticsearch 将在 ./tmp/ 中创建其临时目录。但是，默认情况下，某些强化的 Linux 安装会使用该选项进行挂载。这会阻止 JNA 正常工作。例如，在启动时，JNA 可能无法加载异常或显示类似于 的消息。请注意，异常消息在 JVM 版本之间可能有所不同。此外，依赖于通过 JNA 执行本机代码的 Elasticsearch 组件可能会失败，并显示指示它是 ./tmp /tmp noexe clibffi java.lang.UnsatisfiedLinkerErrorfailed to map segment from shared objectbecause JNA is not available

要解决这些问题，请从文件系统中删除该选项，或者通过设置$ES_TMPDIR环境变量，将 Elasticsearch 配置为为其临时目录使用其他位置。例如：noexec /tmp

export ES_TMPDIR=/usr/share/elasticsearch/tmp
或者，您可以使用JVM 标志配置 JNA 用于其临时文件的路径，也可以使用环境变量配置用于其临时文件的路径。-Djna.tmpdir=<path> libffi LIBFFI_TMPDIR

9. TCP 重新传输超时编辑
每对 Elasticsearch 节点都通过许多 TCP 连接进行通信，这些 TCP 连接一直保持打开状态，直到其中一个节点关闭或节点之间的通信因底层基础架构故障而中断。
TCP 通过对通信应用程序隐藏临时网络中断，通过偶尔不可靠的网络提供可靠的通信。您的操作系统将多次重新传输任何丢失的消息，然后再通知发件人任何问题。Elasticsearch必须在重新传输发生时等待，并且只有在操作系统决定放弃时才能做出反应。因此，用户还必须等待一系列重新传输完成。
大多数 Linux 发行版默认重新传输任何丢失的数据包 15 次。转播以指数方式回退，因此这 15 次转播需要 900 多秒才能完成。这意味着 Linux 需要几分钟才能使用此方法检测网络分区或故障节点。Windows 默认只有 5 次重新传输，对应于大约 6 秒的超时。
Linux 默认设置允许通过可能经历很长一段时间数据包丢失的网络进行通信，但这种默认设置在大多数 Elasticsearch 安装使用的高质量网络上是过度的，甚至是有害的。当集群检测到节点故障时，它会通过重新分配丢失的分片、重新路由搜索以及可能选择新的主节点来做出反应。高可用性集群必须能够及时检测节点故障，这可以通过减少允许的重新传输次数来实现。与远程群集的连接也应该比 Linux 默认允许的更快地检测故障。因此，Linux 用户应减少 TCP 重新传输的最大次数。
您可以通过将以下命令作为 运行来减少 TCP 重新传输的最大次数。五次转播对应于大约六秒的超时。5 root 

sysctl -w net.ipv4.tcp_retries2=5
要永久设置此值，请更新 中的设置。要在重新启动后进行验证，请运行 。
net.ipv4.tcp_retries2 /etc/sysctl.conf
sysctl net.ipv4.tcp_retries2

此设置适用于所有 TCP 连接，并且也会影响与 Elasticsearch 集群以外的系统通信的可靠性。如果您的集群通过低质量网络与外部系统通信，则可能需要为 选择更高的值。因此，Elasticsearch 不会自动调整此设置。net.ipv4.tcp_retries2

10.  在灾难中，快照可以防止永久性数据丢失。快照生命周期管理是对集群进行定期备份的最简单方法。
拍摄快照是备份集群的唯一可靠且受支持的方法。 
您无法通过复制 Elasticsearch 集群节点的数据目录来备份其节点。
没有受支持的方法可以从文件系统级备份还原任何数据。
如果您尝试从此类备份还原集群，则它可能会失败，并报告损坏或丢失文件或其他数据不一致，或者它可能看起来已成功，但以静默方式丢失了某些数据。

11. 开发模式与生产模式编辑
默认情况下，Elasticsearch 假定您处于开发模式。如果上述任何设置配置不正确，日志文件中将写入警告，但您将能够启动并运行 Elasticsearch 节点。

一旦您配置了这样的网络设置 network.host ，Elasticsearch 就会假设您正在迁移到生产环境，并将上述警告升级为异常。这些异常将阻止您的 Elasticsearch 节点启动。这是一项重要的安全措施，可确保不会因服务器配置错误而丢失数据。network.host

12. 报错信息：

[ERROR][o.e.i.g.GeoIpDownloader  ] [node_elastic] exception during geoip databases update
java.net.UnknownHostException: geoip.elastic.co
        at sun.nio.ch.NioSocketImpl.connect(NioSocketImpl.java:567) ~[?:?]
解决方案：
此版本将GeoIp功能默认开启了采集。在默认的启动下是会去官网的默认地址下获取最新的Ip的GEO信息
在elasticsearch.yml中添加配置 
ingest.geoip.downloader.enabled: false
```

