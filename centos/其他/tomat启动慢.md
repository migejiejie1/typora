```shell
tomcat 启动速度慢背后的真相
1. tomcat 启动慢
在线上环境中，我们经常会遇到类似的问题，就是tomcat 启动比较慢，查看内存和cpu,io都是正常的，但是启动很慢，有的时候长达几分钟，这到底是什么原因导致的。

1.1 tomcat 获取随机值阻塞
​ tomcat的启动需要产生session id，这个产生需要通过java.security.SecureRandom生成随机数来实现，随机数算法使用的是”SHA1PRNG”，但这个算法依赖于操作系统的提供的随机数据，在linux系统中，这个值又依赖于/dev/random 和/dev/urandom

/dev/random :阻塞型，读取它就会产生随机数据，但该数据取决于熵池噪声，当熵池空了，对/dev/random 的读操作也将会被阻塞。
/dev/urandom: 非阻塞的随机数产生器，它会重复使用熵池中的数据以产生伪随机数据。这表示对/dev/urandom的读取操作不会产生阻塞，但其输出的熵可能小于/dev/random的。它可以作为生成较低强度密码的伪随机数生成器，不建议用于生成高强度长期密码。
我们通过查看java.security 文件，(我的java版本是1.8.0_131) 发现依赖的是/dev/random

tomcat 启动产生session id 最终依赖的是/dev/random ，/dev/random 又依赖于熵池，

对于熵池，百度百科这样写到

     Linux内核采用熵来描述数据的随机性。熵（entropy）是描述系统混乱无序程度的物理量，一个系统的熵越大则说明该系统的有序性越差，即不确定性越大。在信息学中，熵被用来表征一个符号或系统的不确定性，熵越大，表明系统所含有用信息量越少，不确定度越大。计算机本身是可预测的系统，因此，用计算机算法不可能产生真正的随机数。但是机器的环境中充满了各种各样的噪声，如硬件设备发生中断的时间，用户点击鼠标的时间间隔等是完全随机的，事先无法预测。Linux内核实现的随机数产生器正是利用系统中的这些随机噪声来产生高质量随机数序列。内核维护了一个熵池用来收集来自设备驱动程序和其它来源的环境噪音。理论上，熵池中的数据是完全随机的，可以实现产生真随机数序列。为跟踪熵池中数据的随机性，内核在将数据加入池的时候将估算数据的随机性，这个过程称作熵估算。熵估算值描述池中包含的随机数位数，其值越大表示池中数据的随机性越好。
那么如何查看熵池 的大小,文件 /proc/sys/kernel/random/entropy_avail 保存着 熵池的大小。/proc/sys/kernel/random/poolsize 保存着熵池的最大容量，单位都是bit。

[root@haha cwd]# cat  /proc/sys/kernel/random/entropy_avail
146
总结 tomcat 启动慢的原因是随机数产生遭到阻塞，遭到阻塞的原因是 熵池大小 。

解决方法：

更换产生随机数的源，(也是tomcat的官方文档的启动比较慢的解决办法)
增大熵池 的值
1 . 更换产生随机数的源

​ 官方文档链接

​ 因为/dev/urandom 是非阻塞的随机数产生器，所以我们可以从这边获取，但是生产的随机数的随机性比较低。我们可以在 我们的tomcat启动脚本(catalina.sh)里面添加

JAVA_OPTS="$JAVA_OPTS -Djava.security.egd=file:/dev/./urandom"
或者是更改java的java.security 文件，将securerandom.source=file:/dev/random

securerandom.source=file:/dev/./urandom
​ 注意一下，为什么我们这里使用的路径是"/dev/./urandom",而不是 "/dev/urandom",是因为在java 8之前的版本设置了/dev/urandom ，但是实际还是使用/dev/random，设置为"/dev/./urandom"才能正常使用 "/dev/urandom" ， 这个bug在java8版本已经修复了，如果你是java7版本的话，需要按照上面设置，java8的话可以不用加 "./"。官方bug链接

2 .增大熵池 的值

要增大熵池 的值首先得你的cpu支持DRNG 特性， 如何查看我们的服务器的是否支持DRNG特性？

cat /proc/cpuinfo | grep rdrand
如果不支持的话，那么就只能通过上面的第一种方法来解决了

安装rngd服务(关于rngd服务的介绍)

yum -y install rng-tools
systemctl enable   rngd
systemctl start  rngd
然后我们进行查看我们的熵池 的值,会发现变大了

 cat  /proc/sys/kernel/random/entropy_avail
然后我们启动tomcat 会发现启动速度快很多。

1.2 tomcat 需要部署的web应用程序太多
​ 有的时候，我们tomcat启动比较慢是因为它需要部署的web应用程序太多，但是其中有些应用程序是我们不需要的，比如在webapps下的 doc 、example、ROOT 等等，我们可以将我们不需要的webapps删除，然后再进行发布，这些不需要的web，不仅会占用我们的资源，还有可能是入侵者的入侵对象。如果我们想并行启动多个web应用程序，我们可以Host 的属性 startStopThreads 值设置大于1 ，但这也取决于我们的服务器是不是多核的。如果是多核的建议调大 startStopThreads 的值，但不超过内核数。

1.3 tomcat启动内存不足
​ 如果是项目比较大的话，我们使用默认的参数去启动的tomcat是很有可能内存不足的，我们需要设置JVM，将内存调整，JVM 的最大值和最小值建议是不要相差太大(最好一致.)

在启动脚本catalina.sh加上：

JAVA_OPTS='-server -Xms1024m -Xmx1024m'
具体的内存大小，根据业务调整。

以上就是解决tomcat 启动慢的问题和解决方案，可根据自己的项目情况进行使用。后面也会有一篇tomcat 调优的文章，请大家点波关注哦。


```

