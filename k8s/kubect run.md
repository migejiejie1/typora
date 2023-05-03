```shell
kubectl run 并不是直接创建一个 Pod，而是先创建一个 Deployment 资源（replicas=1），再由与 Deployment 关联的 ReplicaSet 来自动创建 Pod

root@mige:~# kubectl run my-nginx --image=nginx:latest --replicas 2 --labels 'app=nginx' --port 80 
提示信息:
kubectl run --generator=deployment/apps.v1 is DEPRECATED and will be removed in a future version. 
Use kubectl run --generator=run-pod/v1 or kubectl create instead.
翻译:
kubectl run --generator=deployment/apps.v1 已废弃，将在以后的版本中删除。
使用 kubectl run --generator=run-pod/v1 或者 kubectl create 。
 

kubectl run默认情况下，将创建一个Deployment。
完整的命令是：
kubectl run --generator=deployment/apps.v1 <deployment_name> --image=<image_to_use_in_the_container_of_the_deployment's_pod>

因此，kubernetes在执行run命令时将创建的资源由 --generator 标志的值定义。

弃用消息提示的含义是，将删除特定的做法。

因此，在将来的kubernetes版本中，该run命令将默认（并且作为唯一选项）创建pod（而不是部署），即，该命令的完整扩展将变为：

kubectl run --generator=run-pod/v1 <pod_name> --image=<image_of_the_container_of_the_pod>

如果您要创建任何其他kubernetes资源类型，则无法通过run命令来进行创建，因此您将不得不使用显式命令式create或声明式apply -f，后者指向kubernetes yml具有相应资源定义的文件，如

kubernetes apply -f <yaml_file_with_my_deployment.yml>
```

