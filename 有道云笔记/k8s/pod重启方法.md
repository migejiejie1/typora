```shell
pod重启方法:
方式1：使用replace命令替换
可以使用replace结合force选项进行重启，前提是有之前启动时所使用的yaml文件
kubectl replace --force -f busybox-pod.yaml
从结果中可以看出，此种方式实际上替换（replace）的过程是先进行删除然后再次创建的过程

方式2：无yaml文件的replace方法
没有yaml文件时可以使用-o yaml生成，然后再进行replace
执行命令：kubectl get pod pod名称 -n 命名空间名称 -o yaml | kubectl replace --force -f -

方式3：重新创建
相当于上述replace命令的手工执行，可以根据pod生成yaml文件进行创建，先生成创建的yaml文件
kubectl get pod pod名称 -n 命名空间名称 -o yaml > pod.yml
然后删除pod
kubectl delete pod pod名称
然后重新创建pod
kubectl create -f pod.yml

方式4：设定restartPolicy
前提是使用了Deployment或者直接是restartPolicy的设定不是Never，比如是Always，示例如下所示：
restartPolicy: Always
实际使用的时候可以看出，由于此restartPolicy的作用，pod变为completed的时候会立即被重启

方式5：直接删除Pod
前提：使用Deployment等方式的时候，相当于在pod之上又封了一层，所以此时直接删除pod，会有Deployment根据策略进行管控，一般直接删除即可，也可以调整replica来实现类似的效果。
```

