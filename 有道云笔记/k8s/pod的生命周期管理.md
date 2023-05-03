**kubernetes--pod的生命周期管理**

下文基于kubernetes 1.5.2版本编写

**lifecycle**

**概念**

创建资源对象时，可以使用lifecycle来管理容器在运行前和关闭前的一些动作。

lifecycle有两种回调函数：

 PostStart：容器创建成功后，运行前的任务，用于资源部署、环境准备等。

 PreStop：在容器被终止前的任务，用于优雅关闭应用程序、通知其他系统等等。              

**例1、部署代码**

以下示例中，定义了一个Pod，包含一个JAVA的web应用容器，其中设置了PostStart和PreStop回调函数。即在容器创建成功后，复制/sample.war到/app文件夹中。而在容器终止之前，发送HTTP请求到http://monitor.com:8080/waring，即向监控系统发送警告。

具体示例如下：

```yaml
......
containers:
- image: sample:v2  
     name: war
     lifecycle：
      postStart:
       exec:
         command:
          - “cp”
          - “/sample.war”
          - “/app”
      prestop:
       httpGet:
        host: monitor.com
        psth: /waring
        port: 8080
        scheme: HTTP
......
```

**例2、优雅删除资源对象**

当用户请求删除含有pod的资源对象时（如RC、deployment等），K8S为了让应用程序优雅关闭（即让应用程序完成正在处理的请求后，再关闭软件），K8S提供两种信息通知：

 1）、默认：K8S通知node执行docker stop命令，docker会先向容器中PID为1的进程发送系统信号SIGTERM，然后等待容器中的应用程序终止执行，如果等待时间达到设定的超时时间，或者默认超时时间（30s），会继续发送SIGKILL的系统信号强行kill掉进程。

 2）、使用pod生命周期（利用PreStop回调函数），它执行在发送终止信号之前。               

默认情况下，所有的删除操作的优雅退出时间都在30秒以内。kubectl delete命令支持--grace-period=的选项，以运行用户来修改默认值。0表示删除立即执行，并且立即从API中删除pod, 这样一个新的pod会在同时被创建。在节点上，被设置了立即结束的的pod，仍然会给一个很短的优雅退出时间段，才会开始被强制杀死。

具体示例如下：

```yaml
kind: Deployment
metadata:
  name: nginx-demo
  labels:
    app: nginx-demo
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: nginx-demo
    spec:
      containers:
      - name: nginx-demo
        image: centos:nginx
        lifecycle:
          preStop:
            exec:
              # nginx -s quit gracefully terminate while SIGTERM triggers a quick exit
              command: ["/usr/local/nginx/sbin/nginx","-s","quit"]
        ports:
          - name: http
            containerPort: 80
```

**Init Container**

**概念**

在K8S的POD中，有两类容器，一类是系统容器（POD Container），一类是用户容器（User Container），在用户容器中，现在又分成两类容器，一类是初始化容器（Init Container），一类是应用容器（App Container）。

Init Container就是做初始化工作的容器。可以有一个或多个，如果多个按照定义的顺序依次执行，只有所有的执行完后，主容器才启动。由于一个Pod里的存储卷是共享的，所以Init Container里产生的数据可以被主容器使用到。

Init Container可以在多种K8S资源里被使用到如Deployment、DaemonSet, StatefulSet、Job等，但都是在Pod启动时，在主容器启动前执行，做初始化工作。

**应用场景**

**等待其它模块Ready**

比如使用apache部署web服务，需要做一些准备工作（例如从git服务器上拉取代码、检查运行环境是否到位等），可以在运行Web服务的Pod里使用一个Init Container，去执行准备工作，完成后Init Container结束退出，然后启动正在的apache容器。

**做初始化配置**

比如集群里检测所有已经存在的成员节点，为主容器准备好集群的配置信息，这样主容器起来后就能用这个配置信息加入集群。

**例子**

```yaml
cat << EOF > lykops-deploy-init-container.yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: lykops-deploy-init-container
  labels:
    project: lykops
    app: init-container
    version: v1         
  annotations:
    pod.beta.K8S.io/init-containers: 
    - name: apache-web,
      image: web:apache,
      command: ["sh", "httpd -t"]
spec:
  replicas: 1
  minReadySeconds: 30
  selector:
    matchLabels:
      name: lykops-deploy-init-container
      project: lykops
      app: init-container
      version: v1
  template:
    metadata:
      labels:
        name: lykops-deploy-init-container
        project: lykops
        app: init-container
        version: v1
    spec:
      containers:
      - name: webapache
        image: web:apache
        command: [ "sh", "/etc/run.sh" ]
        ports:
        - containerPort: 80
          name: http
          protocol: TCP
EOF
kubectl create -f lykops-deploy-init-container.yaml
```

**应用程序健康检查**

K8S的应用程序健康检查分为livenessProbe和readinessProbe，两者相似，但两者存在着一些区别。

livenessProbe在服务运行过程中检查应用程序是否运行正常，不正常将杀掉进程；而readness Probe是用于检测应用程序启动完成后是否准备好对外提供服务，不正常继续检测，直到返回成功为止。

**livenessProbe**

许多应用程序经过长时间运行，最终过渡到无法运行的状态，除了重启，无法恢复。通常情况下，K8S会发现应用程序已经终止，然后重启应用程序/pod。

有时应用程序可能因为某些原因（后端服务故障等）导致暂时无法对外提供服务，但应用软件没有终止，导致K8S无法隔离有故障的pod，调用者可能会访问到有故障的pod，导致业务不稳定。K8S提供livenessProbe来检测应用程序是否正常运行，并且对相应状况进行相应的补救措施。

**readinessProbe**

在没有配置readinessProbe的资源对象中，pod中的容器启动完成后，就认为pod中的应用程序可以对外提供服务，该pod就会加入相对应的service，对外提供服务。但有时一些应用程序启动后，需要较长时间的加载才能对外服务，如果这时对外提供服务，执行结果必然无法达到预期效果，影响用于体验。

比如使用tomcat的应用程序来说，并不是简单地说tomcat启动成功就可以对外提供服务的，还需要等待spring容器初始化，数据库连接连接上等等。对于spring boot应用，默认的actuator带有/health接口，可以用来进行启动成功的判断。

**检测方式**

**exec-命令**

在用户容器内执行一次命令，如果命令执行的退出码为0，则认为应用程序正常运行，其他任务应用程序运行不正常。

```yaml
……
      livenessProbe:
        exec:
          command:
          - cat
          - /home/laizy/test/hostpath/healthy
……
```

**TCPSocketAction**

将会尝试打开一个用户容器的Socket连接（就是IP地址：端口）。如果能够建立这条连接，则认为应用程序正常运行，否则认为应用程序运行不正常。

```yaml
……      
livenessProbe:
tcpSocket:
              port: 8080
……
```

**HTTPGetAcction**

调用容器内Web应用的web hook，如果返回的HTTP状态码在200和399之间，则认为应用程序正常运行，否则认为应用程序运行不正常。每进行一次HTTP健康检查都会访问一次指定的URL。 …… httpGet: #通过httpget检查健康，返回200-399之间，则认为容器正常 path: / #URI地址 port: 80 #端口号 #host: 127.0.0.1 #主机地址 scheme: HTTP #支持的协议，http或者https httpHeaders：’’ #自定义请求的header ……

**例子**

```yaml
cat << EOF > inessprobe.yaml
apiVersion: v1 
kind: ReplicationController 
metadata: 
  name: inessprobe
  labels: 
    project: lykops
    app: inessprobe
    version: v1  
spec:
  replicas: 6
  selector: 
    project: lykops
    app: inessprobe
    version: v1
    name: inessprobe
  template: 
    metadata:
      labels: 
        project: lykops
        app: inessprobe
        version: v1
        name: inessprobe
    spec:
      restartPolicy: Always 
      containers:
      - name: inessprobe
        image: web:apache 
        imagePullPolicy: Never 
        command: ['sh',"/etc/run.sh" ] 
        ports:
        - containerPort: 80
          name: httpd
          protocol: TCP
        readinessProbe:
          httpGet:
            path: /
            port: 80
            scheme: HTTP
          initialDelaySeconds: 120 
          periodSeconds: 15 
          timeoutSeconds: 5
        livenessProbe: 
          httpGet: 
            path: /
            port: 80
            scheme: HTTP
          initialDelaySeconds: 180 
          timeoutSeconds: 5 
          periodSeconds: 15 
EOF

cat << EOF > inessprobe-svc.yaml
apiVersion: v1
kind: Service
metadata:
  name: inessprobe
  labels:
    project: lykops
    app: inessprobe
    version: v1
spec:
  selector:
    project: lykops
    app: inessprobe
    version: v1
  ports:
  - name: http
    port: 80
    protocol: TCP
EOF

kubectl create -f inessprobe-svc.yaml
kubectl create -f inessprobe.yaml 

```

```shell
参数说明initialDelaySeconds：容器启动后第一次执行探测是需要等待多少秒。
periodSeconds：执行探测的频率。默认是10秒，最小1秒。
timeoutSeconds：探测超时时间。默认1秒，最小1秒。
successThreshold：探测失败后，最少连续探测成功多少次才被认定为成功。默认是1。对于liveness必须是1。最小值是1。
failureThreshold：探测成功后，最少连续探测失败多少次才被认定为失败。默认是3。最小值是1。
```

