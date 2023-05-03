```shell
[K8S] 如何利用terminationGracePeriodSeconds 优雅地关闭你的服务？
前言
K8S 是一个管理容器的好框架，可是， 用了K8S就能做到服务在版本升级过程中的零停机了吗？非也，如果不正确的使用或者配置K8S，可能会导致在升级过程中部分用户请求失败。
记得上一篇说了如何 readiness probe 在 K8S 的滚动升级中的重要性。今天继续说说K8S滚动升级中用到的的另一个重要参数: terminationGracePeriodSeconds.
什么是 terminationGracePeriodSeconds
解释这个参数之前，先来回忆一下K8S滚动升级的步骤:
K8S首先启动新的POD
K8S等待新的POD进入Ready状态
K8S创建Endpoint，将新的POD纳入负载均衡
K8S移除与老POD相关的Endpoint，并且将老POD状态设置为Terminating，此时将不会有新的请求到达老POD
同时 K8S 会给老POD发送SIGTERM信号，并且等待 terminationGracePeriodSeconds 这么长的时间。(默认为30秒)
超过terminationGracePeriodSeconds等待时间后， K8S 会强制结束老POD
看到这里，我想大家应该明白了，terminationGracePeriodSeconds 就是K8S给你程序留的最后的缓冲时间，来处理关闭之前的操作。
问题剖析
或许你会问，如果不配置或者不处理这个，有什么问题？假象一下下面的场景：
K8S会给你30秒的缓冲，假如，你有一个操作需要很长的时间，超过了30秒，那么这个请求是不是就会失败？
如果你的程序没有处理SIGTERM信号，默认是收到这个信号会终止程序，那么当前Pod里面现存的所有用户请求都会失败
解决问题
既然知道可能带来的问题了，那么怎么解决呢？明白这个参数的意义，那就很好解决了。
首先 terminationGracePeriodSeconds 要设置一个合适的值，至少保证所有现存的request能被正确处理并返回
你的程序需要处理SIGTERM信号，并且保证所有事务完成后再关闭程序。参见例子: 
springboot-graceful-terminator
注意： 如果你的程序不处理SIGTERM信号，JVM会默认直接关闭，所以不能保证所有现存事务被正确处理，这样会导致部分现存请求失败
```

