**k8s 生命周期 优雅关闭pod**

**如何利用termination GracePeriodSeconds(终止宽限期)优雅地关闭你的服务**

当涉及到分布式系统,处理故障是关键。[Kubernetes](https://so.csdn.net/so/search?from=pc_blog_highlight&q=Kubernetes)通过利用可以监视系统状态并重新启动已停止执行的服务的控制器（controllers）来解决这个问题。另一方面，Kubernetes通常可以强制终止您的应用程序，作为系统正常运行的一部分。

在容器出现之前，大多数应用运行在虚拟机或者物理机上。如果应用程序崩溃，启动替换程序需要很长时间。如果您只有一台或两台机器来运行应用程序，那么这种恢复时间是不可接受的。

相反，在崩溃时使用进程级监控来重新启动应用程序变得很常见。如果应用程序崩溃，监视进程可以捕获退出代码并立即重新启动应用程序。

随着像Kubernetes这样的系统的出现，不再需要进程监控系统，因为Kubernetes可以处理重启崩溃的应用程序。Kubernetes使用事件循环来确保容器和节点等资源是健康的。这意味着您不再需要手动运行这些监视进程。如果资源未通过健康检查，Kubernetes会自动启动一个替代品。

Kubernetes终止生命周期Kubernetes不仅可以监控崩溃应用程序，它还可以创建更多应用程序副本，以便在多台计算机上运行，更新应用程序，甚至可以同时运行多个版本的应用程序！

这意味着Kubernetes可以终止一个完全健康的容器有很多原因。如果您使用滚动更新更新部署，Kubernetes会在启动新pod时慢慢终止旧pod。如果drain一个节点，Kubernetes将终止该节点上的所有pod。如果节点资源不足，Kubernetes将终止pod以释放这些资源

您的应用程序要优雅地处理终止是至关重要的，可以最终用户受到的影响最小，并且恢复时间尽可能快！

实际上，这意味着您的应用程序需要处理SIGTERM消息并在收到它时开始关闭。这意味着保存所有需要保存的数据，关闭网络连接，完成剩下的任何工作以及其他类似任务。

一旦Kubernetes决定终止您的Pod，就会发生一系列事件。

**让我们看看Kubernetes终止生命周期的每一步。**

1 - K8S 启动新POD。此时，新的POD等待启动

2 - K8S等待新POD进入Ready(Running) 状态。此时，pod状态为Running。

3 - K8S创建Endpoint。此时，k8s创建endpoint，将新服务纳入负载均衡。

4 - Pod设置为”Terminating”状态，并从所有服务的Endpoints列表中删除。此时，Pod停止获得新的流量。但在Pod中运行的容器不会受到影响。

5 - preStop Hook被执行preStop Hook是一个发送到Pod中的容器特殊命令或Http请求。

如果您的应用程序在接收SIGTERM时没有正常关闭，您可以使用preStop Hook来触发正常关闭。接收SIGTERM时大多数程序都会正常关闭，但如果您使用的是第三方代码或管理的系统无法控制，则preStop Hook是在不修改应用程序的情况下触发正常关闭的好方法。

6 - SIGTERM信号被发送到Pod此时，Kubernetes将向pod中的容器发送SIGTERM信号。这个信号让容器知道它们很快就会关闭。

您的代码应该监听此事件并在此时开始干净利落关闭。这可能包括停止任何长期连接（如数据库连接或WebSocket流），保存当前状态或其它类似的事情。

即使您使用preStop Hook，如果您发送SIGTERM信号，测试应用程序会发生什么情况也很重要，以确保您对生产环境并不感到惊讶！

7 - Kubernetes等待优雅的终止此时，Kubernetes等待指定的时间称为优雅终止宽限期。默认情况下，这是30秒。值得注意的是，这与preStop Hook和SIGTERM信号并行发生。Kubernetes不会等待preStop Hook完成。

如果你的应用程序完成关闭并在terminationGracePeriod(终止宽限期)完成之前退出，Kubernetes会立即进入下一步。

如果您的Pod通常需要超过30秒才能关闭，请确保增加优雅终止宽限期。您可以通过在Pod YAML中设置terminationGracePeriodSeconds选项来实现。例如，要将其更改为60秒：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  namespace: default
spec:
  containers:
  - name: nginx
    image: nginx
  terminationGracePeriodSeconds: 60
	```
```

8 - SIGKILL信号被发送到Pod，并删除Pod

如果容器在优雅终止宽限期后仍在运行，则会发送SIGKILL信号并强制删除。与此同时，所有的Kubernetes对象也会被清除。





结论

Kubernetes可以出于各种原因终止pod，并确保您的应用程序优雅地处理这些终止，这是创建稳定系统和提供出色用户体验的核心。



译者注：

kubernetes文档指出，有些步骤是同时执行的。因此有可能会导致该Pod仍然列在服务的Endpoints中并仍然接收流量，而它已经收到SIGTERM并且已经停止，因此负载均衡器上可能会有一些Http 504。目前解决这个问题可以使用preStop Hook 在容器收到SIGTERM时sleep一段时间，以确终止期间的流量可以正确处理。设置方式：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  namespace: default
spec:containers:
  name: nginx
  image: nginx
  lifecycle:
    preStop:
      exec:
        command:
          - sleep
          - 30
  terminationGracePeriodSeconds: 60
```

特别说明：preStop Hook并不会影响SIGTERM的处理，因此有可能preStopHook还没有执行完就收到SIGKILL导致容器强制退出。因此如果preStop Hook设置了n秒，需要设置terminationGracePeriodSeconds为terminationGracePeriodSeconds+n秒。