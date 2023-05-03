```shell
架构部署:

1.docker私有镜像仓库
2.jenkins镜像
3.存储卷
4.rancher容器管理
5.k8s容器编排

6.eureka
7.config
8.zuul
9.oauth
10.client

11.mysql
12.redis




---------------------------------------------------------------------------
.170部署：
1.docker私有镜像仓库
2.jenkins镜像
3.存储卷
4.rancher容器管理
5.k8s容器编排


.171部署：
6.eureka1
7.config1
8.zuul1
9.oauth1
10.client1
11.mysql1
12.redis1



.172部署：
6.eureka2
7.config2
8.zuul2
9.oauth2
10.client2
11.mysql2
12.redis2
12.redis3









firewall-cmd --zone=public --add-port=22/tcp --permanent
firewall-cmd --reload


---------------------------------------------------------------------------
0.0安装linux
装完后没网
vi /ect/sysconfig/network-scripts/ifcfg-ens33
ONBOOT=yes
:wq
systemctl restart network

安装ifconfig
yum search ifconfig
yum install -y net-tools.x86_64

yum -y install wget

yum install -y lrzsz













0.0安装docker
去清华大学开源镜像站上下载指定仓库文件
https://mirrors.tuna.tsinghua.edu.cn/
docker-ce
linux
centos
cd /etc/yum.repos.d/
下载 wget https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/centos/docker-ce.repo
修改文件里的下载访问路径
vi docker-ce.repo
:%s@https://download.docker.com/@https://mirrors.tuna.tsinghua.edu.cn/docker-ce/@

:wq

查看
yum repolist

安装
yum install docker-ce

systemctl enable docker

------------这一步后开始复制linux

修改镜像仓库代理地址
vi /etc/docker/daemon.json
{
"registry-mirrors": ["http://86d2a50b.m.daocloud.io"]
}

systemctl  restart docker





https://www.jianshu.com/p/40f4fbe1ec22
第一步：
1.0安装rancher
：如果是1.x系列的，镜像名为rancher/server，而2.x是rancher/rancher
cd /
docker pull rancher/rancher


1.1查看镜像列表
docker image ls

1.2查看rancher镜像详细信息
docker inspect rancher/rancher:latest

1.3当前下载的版本为
CATTLE_SERVER_VERSION=v2.2.7

1.4显而易见，rancher镜像主要有两个volume目录，默认方式是采用匿名卷的方式。


1.5执行如下命令，在宿主机创建两个挂载目录
mkdir -p /water/runfile/docker_volume/rancher_home/rancher
mkdir -p /water/runfile/docker_volume/rancher_home/auditlog


1.6使用挂载到指定的主机目录方式来进行数据卷持久化同时启动rancher
docker run -d \
  --restart=always \
  -p 80:80 \
  -p 443:443 \
  --mount  'src=rancher-data,target=/var/lib/rancher' \
  --mount  'src=rancher-log,target=/var/log' \
  --name rancher -e JAVA_OPTS="-Xmx2048m" \
  --privileged \
  rancher/rancher

docker run -d \
  --restart=always \
  -p 80:80 \
  -p 443:443 \
-v /water/runfile/docker_volume/rancher_home/rancher:/var/lib/rancher \
-v /water/runfile/docker_volume/rancher_home/auditlog:/var/log/auditlog \
--name rancher -e JAVA_OPTS="-Xmx1024m" rancher/rancher


1.7执行如下命令查看我们刚才启动的容器信息
docker container ls


1.8增加开放端口
firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --zone=public --add-port=443/tcp --permanent
firewall-cmd --reload


1.9为admin账户设置默认密码
admin
admin

1.10设置rancher server url，可以是IP地址或主机名，但是你要保证群集的每个节点都能够连接到它

192.168.232.140
点击Save URL按钮后，即将跳转到rancher管理主页。


1.11通过右下角的语言选项来选择语言，这里我们选择简体中文。


1.12创建集群
点击图中的添加集群按钮，进入添加集群页面


1.13选择"添加主机自建Kubernetes集群"/"CUSTOM"

1.14输入你的集群名称
SpringCloud

1.15下面的成员角色、集群选项等几个tab都可展开进行详细的配置，这里我们不进行配置

1.16网络组件
选择Canal，直接点击下一步


1.17勾选上所有的主机角色


修改主机名，不然重名的话k8s连接失败
hostnamectl set-hostname 140
hostnamectl set-hostname 141
hostnamectl set-hostname 142

1.18将页面中第二步里显示的命令复制到宿主机进行执行,这里的命令是页面动态生成，所以没有复制到这里


1.19执行成功后我们的浏览器中会提示如下图所示的信息[1台新主机注册成功]


1.20点击完成按钮后，会跳转到集群首页

这时候你可以稍作等待，这个时间可能有点长，
因为这期间会在后台给我们pull多个镜像并会启动多个容器。
直到集群处于Active状态(如下图)时，说明集群创建成功了。
注：如果等太久，则给大一点内存。


1.21可以通过命令看看刚才集群创建过程中都为我们拉取了哪些镜像和启动了哪些容器
docker image ls
docker container ls



https://www.jianshu.com/u/4f36546f7853


--------------------------------------------
https://www.jianshu.com/p/01bb90bfcabb
1.22上一步创建的集群中给我们分配了两个项目Default和System，接下来我们在Default项目中部署我们的服务

1.23点击部署服务按钮


linux修改静态ip
https://blog.csdn.net/xiaozelulu/article/details/80495525


安装时间同步插件
https://blog.csdn.net/vah101/article/details/91868147


etcd - 这些主机可以用来保存集群的数据。
controlplane - 这些主机可以用来存放运行K8s所需的Kubernetes API服务器和其他组件。
worker - 这些是您的应用程序可以部署的主机。

1.24连接另一台服务器141,点集群springcloud升级，去掉Etcd,勾选Worker和Control,复制粘贴命令
140中执行：
firewall-cmd --zone=public --add-port=2379/tcp --permanent
firewall-cmd --zone=public --add-port=2380/tcp --permanent
firewall-cmd --zone=public --add-port=6443/tcp --permanent
firewall-cmd --zone=public --add-port=10250/tcp --permanent
firewall-cmd --reload



141中执行：
firewall-cmd --zone=public --add-port=2379/tcp --permanent
firewall-cmd --zone=public --add-port=2380/tcp --permanent
firewall-cmd --zone=public --add-port=6443/tcp --permanent
firewall-cmd --zone=public --add-port=10250/tcp --permanent
firewall-cmd --reload


1.25
在141中执行
vi /etc/docker/daemon.json 
{
"registry-mirrors": ["http://86d2a50b.m.daocloud.io"]
}


1.26连接另一台服务器142,点集群springcloud升级，去掉Etcd,勾选Worker和Control,复制粘贴命令
142中执行：
firewall-cmd --zone=public --add-port=2379/tcp --permanent
firewall-cmd --zone=public --add-port=2380/tcp --permanent
firewall-cmd --zone=public --add-port=6443/tcp --permanent
firewall-cmd --zone=public --add-port=10250/tcp --permanent
firewall-cmd --reload


1.27
在142中执行
vi /etc/docker/daemon.json 
{
"registry-mirrors": ["http://86d2a50b.m.daocloud.io"]
}
重启docker
systemctl restart docker


1.28导出etcd镜像去142加载，速度快些
142安装rz\sz命令:yum -y install lrzsz

141上导出镜像
docker save  rancher/coreos-etcd:v3.3.10-rancher1 -o etcd.tar
142上安装镜像
docker load -i etcd.tar

1.29创建rancher标签
iprange=170
iprange=171


1.30给142加上标签
iprange=172


1.31给141和142加上标签
deploy=worker





2.0安装jenkins
通过Rancher部署服务来完成jenkins的安装
首先下载镜像
当然，从rancher的部署页中启动可以自动为我们下载部署的镜像
但我们这里为了更清晰的使用，采用手动pull镜像的方式，执行如下命令：
docker pull jenkins/jenkins:alpine


当然如果你有镜像提供者的Dockerfile那就更好了
有了Dockerfile，你可以更为清楚的了解这个镜像的制作过程，方便后面的使用。


2.1接下来，在宿主机创建挂载文件夹
mkdir -p /water/runfile/docker_volume/jenkins_home


2.2因为 jenkins user - uid 1000(容器内使用的用户可能没有主机上文件夹的权限)，所以这里我们更改目录用户组及用户
#chown -R 1000:1000 /water/runfile/docker_volume/jenkins_home
#一下三行不需要
#firewall-cmd --zone=public --add-port=30000/tcp --permanent
#firewall-cmd --zone=public --add-port=30001/tcp --permanent
#firewall-cmd --reload

2.3在rancher的部署页中做如下操作
	1.输入名称 jenkins
	2.输入镜像名称 jenkins/jenkins:alpine
	3.添加端口映射 30000(主机):8080(容器)和 30001(主机):50000(容器)
	4.添加一个路径映射卷，卷名为jenkins-home
	主机路径的话就是填入我们先前创建的/water/runfile/docker_volume/jenkins_home目录路径，
	选择为现有目录并映射到容器路径/var/jenkins_home目录路径
	4.0.1后面maven的配置文件
	4.1因为要在jenkins下执行docker命令，所以再挂载两个宿主机的路径/var/run/docker.sock:/var/run/docker.sock /usr/bin/docker:/usr/bin/docker
	4.2因为jenkins里要执行docker命令，所以将用户设置为uid为0的用户启动。点击高级选项，命令，用户uid填写0
	5.点击启动按钮

这几个步骤其实反应到我们docker容器中就如同下面的命令：
docker run -d --restart unless-stopped --name jenkins \
    -p 30000:8080 -p 30001:50000 \
    -v /water/runfile/docker_volume/jenkins_home:/var/jenkins_home \
    jenkins/jenkins:alpine


2.4刷新等待，直到等到服务状态为Active后说明服务部署成功


2.5你可以点击如下图中标红的连接http://192.168.232.140:30000/ 就是我们先前对8080映射到了主机30000端口


2.6初始化Jenkins
到我们的主机映射目录去查看密码
cat /water/runfile/docker_volume/jenkins_home/secrets/initialAdminPassword

当然也可以在rancher中操作选项里操作执行命令行，在这个里面操作命令就是基于容器内部文件路径了


2.7选择"按照系统建议的插件"，一直等到插件安装完成，这可能需要几分钟时间
非常遗憾默认的插件里没有我们用到的maven，后面需要自己配置插件，
从这里我们也要思考一下以后在做项目中，能够考虑用Gradle来替代maven，这也许是一个流行趋势


2.8创建用户设置密码
root
root



2.9安装maven
在全局工具配置中安装maven， 选择一个合适的版本，勾选自动安装，之后直接保存，
需注意的是，现在jenkins并不会立即给你安装maven软件


2.10maven名字为：jenkins-in-maven


2.11接下来我们在插件管理中查找maven插件，
可以在浏览器中使用ctrl+f快捷键来快速定位插件，
选择好maven integration插件，然后点击直接安装


2.12创建一个maven项目进行一下构建，这里构建肯定会失败，因为没有指定仓库等信息
配置git地址，maven的pom spring-base/spring-eureka/pom.xml 命令clean package -Dmaven.test.skip=true


2.13构建后会自动下载maven.zip

2.14在正式构建项目代码前，需要配置maven的仓库等信息
在宿主机执行如下命令进入jenkins-in-maven目录(先前安装的默认目录) 并通过ls查看目录下清单列表
cd /water/runfile/docker_volume/jenkins_home/tools/hudson.tasks.Maven_MavenInstallation/jenkins-in-maven



2.15指定Repository目录和mirror等配置
vi conf/settings.xml
在相应位置加入如下配置，并保存退出vi模式

<localRepository>
/var/jenkins_home/tools/hudson.tasks.Maven_MavenInstallation/jenkins-in-maven/repository
</localRepository>

与

<mirror>
  <id>alimaven</id>
  <name>aliyun maven</name>
  <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
  <mirrorOf>central</mirrorOf>
</mirror>


在<mirrors>标签下



2.16配置好后继续点击构建
构建完成后找到jar位置
cd /water/runfile/docker_volume/jenkins_home/workspace/springcloud-eureka/spring-base/spring-eureka/target








Rancher及Docker快速上手指南（二）
https://blog.csdn.net/weixin_33896069/article/details/89592679
Rancher
https://192.168.232.140/p/c-drj2c:p-bj5j9/workloads
Docker-Eureka集群搭建
https://blog.csdn.net/qq_26663155/article/details/82785740

--------------------------------------------
3创建私有仓库
docker pull registry

3.1因为Docker从1.3.X之后，与docker registry交互默认使用的是https，
然而此处搭建的私有仓库只提供http服务，所以当与私有仓库交互时就会报上面的错误
"registry-mirrors": ["https://registry.docker-cn.com"],
insecure-registries:是为解决https的问题所以配置

141 142上执行
vi /etc/docker/daemon.json 
{
"registry-mirrors": ["http://86d2a50b.m.daocloud.io"],
"insecure-registries": ["192.168.232.140:5000"]
}

systemctl  restart docker

mkdir -p /water/runfile/docker_volume/registry

3.1到rancher部署registry容器，
服务名 registry
镜像名registry:latest
端口5000
映射路径
/water/runfile/docker_volume/registry:/var/lib/registry

高级选项 网络 是否选用主机网络选择是,后台会在启动时加上--net=host命令,表示不虚拟出网卡,不然erueka注册ip有问题

点击启动

firewall-cmd --zone=public --add-port=5000/tcp --permanent
firewall-cmd --reload


实际运行的命令如下：
docker run -d -p 5000:5000 
--name=dockerregistry 
--privileged=true 
-v /water/runfile/docker_volume/registry:/var/lib/registry registry 






4.0创建项目Dockerfile文件生成镜像
在spring-eureks项目main目录下新建docker目录，新建Dockerfile文件
FROM java:8

#将本地文件夹挂载到当前容器
VOLUME /tmp

ADD spring-eureka-0.0.1-SNAPSHOT.jar app.jar
RUN ["/bin/bash","-c","touch /app.jar"]

#指定JAVA 环境变量
ENV JAVA_HOME /jdk/jre
ENV PATH $PATH:$JAVA_HOME/bin
ENV CLASSPATH .:$JAVA_HOME/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
#开放8000端口
EXPOSE 8000
#配置容器启动后执行的命令
ENTRYPOINT ["java","-jar","/app.jar"]

4.1在jenkins配置里，配置根据Dockerfile创建镜像并上传到私有镜像仓库

项目名称springcloud-eureka
spring-base/spring-eureka/pom.xml
clean package -Dmaven.test.skip=true

点击maven的高级按钮
DEFAULT
Settings file 选择文件系统中的settings文件
/var/jenkins_home/tools/hudson.tasks.Maven_MavenInstallation/jenkins-in-maven/conf/settings.xml



cp /var/jenkins_home/workspace/springcloud-eureka/spring-base/spring-eureka/src/main/docker/Dockerfile /var/jenkins_home/workspace/springcloud-eureka/spring-base/spring-eureka/target/
点击保存按钮
#下面三行不要了，用jenkins插件实现的
#cd /var/jenkins_home/workspace/springcloud-eureka/spring-base/spring-eureka/target/
#/bin/docker build -t 192.168.232.140:5000/spring-eureka .
#/bin/docker push 192.168.232.140:5000/spring-eureka



4.2在jenkins里安装云 docker插件
系统设置->新增一个云docker,把前面挂载的docker.sock 文件写入Docker Host URI 中：unix:///var/run/docker.sock，
可以点击 右中位置的TestConnection按钮进行测试是否连接成功

4.3接下来配置eureka服务，Post Steps里添加一个Build/Publish Docker Image选项
Directory for Dockerfile填写. (就一个点) 填写：/var/jenkins_home/workspace/springcloud-eureka/spring-base/spring-eureka/target/
Cloud选择先前添加docker云配置的名称，
然后输入镜像名192.168.232.140:5000/spring-eureka
Push image打勾
Clean local images打勾



4.4在rancher里配置集群的私有仓库地址
在集群命令行中执行：
kubectl create secret docker-registry mysecret --docker-server=192.168.232.140:5000 --docker-username=root --docker-password=lang --docker-email=lang@lang.com


4.5部署eureka服务，部署工作负载
spring-eureka
192.168.232.140:5000/spring-eureka
端口 8000 8000
主机调度 deploy worker
高级选项 网络 是否选用主机网络选择是,后台会在启动时加上--net=host命令,表示不虚拟出网卡,不然erueka注册ip有问题


firewall-cmd --zone=public --add-port=8000/tcp --permanent
firewall-cmd --reload

点击启动




5.0给eureka1设置只在iprange=171标签的主机上运行
修改配置提交git给eureka2设置只在iprange=172标签的主机上运行

5.1
将registry和jenkins分配到拥有指定标签170的主机上
将eureka服务分配到拥有指定标签171的主机上



6.0创建config项目Dockerfile文件生成镜像
在spring-config项目main目录下创建Dockerfile文件并配置jenkins里的打包
jenkins里面复制一个maven项目即可，改一下配置和路径
springcloud-config
spring-base/spring-config/pom.xml
clean package -Dmaven.test.skip=true

点击maven的高级按钮
DEFAULT
Settings file 选择文件系统中的settings文件
/var/jenkins_home/tools/hudson.tasks.Maven_MavenInstallation/jenkins-in-maven/conf/settings.xml

cp /var/jenkins_home/workspace/springcloud-config/spring-base/spring-config/src/main/docker/Dockerfile /var/jenkins_home/workspace/springcloud-config/spring-base/spring-config/target/


/var/jenkins_home/workspace/springcloud-config/spring-base/spring-config/target/
Cloud选择先前添加docker云配置的名称，
然后输入镜像名192.168.232.140:5000/spring-config
Push image打勾
Clean local images打勾




6.1在rancher里创建config服务
spring-config
192.168.232.140:5000/spring-config
8888 8888
主机调度 deploy worker
高级选项 网络 是否选用主机网络选择是,后台会在启动时加上--net=host命令,表示不虚拟出网卡,不然erueka注册ip有问题
配置linux本机的端口映射，网络防火墙
firewall-cmd --zone=public --add-port=8888/tcp --permanent
firewall-cmd --reload

config部署成功


7.0创建spring-zuul的Dockerfile文件并配置jenkins里的打包
项目名称springcloud-zuul
spring-zuul/pom.xml
clean package -Dmaven.test.skip=true

执行shell->
cp /var/jenkins_home/workspace/springcloud-zuul/spring-zuul/src/main/docker/Dockerfile /var/jenkins_home/workspace/springcloud-zuul/spring-zuul/target/




Directory for Dockerfile->/var/jenkins_home/workspace/springcloud-zuul/spring-zuul/target/

Image->192.168.232.140:5000/spring-zuul
Clean local images勾选，推到私有仓库后本地不留


7.1在rancher里创建zuul服务
spring-zuul
192.168.232.140:5000/spring-zuul
8001 8001
主机调度 deploy worker
高级选项 网络 是否选用主机网络选择是,后台会在启动时加上--net=host命令,表示不虚拟出网卡,不然erueka注册ip有问题
#这个应用也不需要外放访问，只要再做一个nginx镜像将路径转发到这个机器即可
配置linux本机的端口映射，网络防火墙
firewall-cmd --zone=public --add-port=8001/tcp --permanent
firewall-cmd --reload

zuul部署成功








8.0部署reids镜像
在141上部署redis1,在142上部署redis2和redis3

在140上执行，也可以在rancher直接部署，它会自动下载镜像
docker pull redis:5.0.5-alpine3.10

按照惯例查看一下镜像详细信息
docker image inspect redis:5.0.5-alpine3.10

推送到私有仓库去
docker tag redis:5.0.5-alpine3.10 192.168.232.140:5000/redis:5.0.5-alpine3.10
docker push 192.168.232.140:5000/redis:5.0.5-alpine3.10
docker rmi 192.168.232.140:5000/redis:5.0.5-alpine3.10
docker rmi redis:5.0.5-alpine3.10

8.1Redis通过RDB和AOF两种方式来进行数据的持久化，先创建一个主机目录作为持久化目录进行数据挂载





8.2rancher部署redis服务之前，先做好远程挂载卷，
https://www.cnblogs.com/st-jun/p/7742560.html
因为现在的目录和文件都在140上，而redis是部署到141,142动态部署，不可能手动去创建文件夹和conf文件，所以在动态主机上挂载远程存储卷。

8.2.1服务器端安装NFS服务
140上执行
判断有没有装NFS，rpm -qa nfs-utils rpcbind

如果没有，那就安装NFS服务的nfs-unitls和rpcbind（会自动同时自动安装rpcbind）
yum -y install nfs-utils

启动rpcbind服务（一定要先启动rpcbind服务再启动nfs服务）
systemctl status rpcbind

systemctl stop rpcbind
systemctl stop nfs-utils
systemctl start rpcbind
systemctl start nfs-utils
systemctl enable rpcbind
systemctl enable nfs-utils


8.2.2
nfs除了主程序端口2049和rpcbind的端口111是固定以外，还会使用一些随机端口，以下配置将定义这些端口，以便配置防火墙
vi /etc/sysconfig/nfs
#结尾追加端口配置
MOUNTD_PORT=4001
STATD_PORT=4002
LOCKD_TCPPORT=4003
LOCKD_UDPPORT=4003
RQUOTAD_PORT=4004

:wq

vi /etc/exports
/water/runfile/docker_volume 192.168.232.140/24(rw,sync,wdelay,hide,no_subtree_check,sec=sys,secure,root_squash,no_root_squash)
#这里可以配置多个路径，我只配置了/water/runfile/docker_volume


rpc.nfsd 8
rpc.mountd

exportfs -r
#使配置生效

exportfs
#可以查看到已经ok


firewall-cmd --zone=public --add-port=2049/udp --permanent
firewall-cmd --zone=public --add-port=2049/tcp --permanent
firewall-cmd --zone=public --add-port=111/udp --permanent
firewall-cmd --zone=public --add-port=111/tcp --permanent
firewall-cmd --zone=public --add-port=4001/udp --permanent
firewall-cmd --zone=public --add-port=4001/tcp --permanent
firewall-cmd --zone=public --add-port=4004/udp --permanent
firewall-cmd --zone=public --add-port=4004/tcp --permanent
firewall-cmd --reload



systemctl restart nfs-config
systemctl restart nfs-idmap
systemctl restart nfs-lock
systemctl restart nfs-server
systemctl enable nfs-config
systemctl enable nfs-idmap
systemctl enable nfs-lock
systemctl enable nfs-server

rpcinfo -p


在nfs客户机上执行：
141、142上执行：
yum -y install nfs-utils

systemctl stop rpcbind
systemctl stop nfs-utils
systemctl start rpcbind
systemctl start nfs-utils
systemctl enable rpcbind
systemctl enable nfs-utils




给141主机添加标签redis=redisM,给142主机添加标签redis=redisS

-----------------------------------------------------------------------
这里不用，这是单节点的redis。使用下面的reids集群
------------------------
8.2.3 Rancher2.0中使用NFS存储,在集群中创建持久卷（添加PV）
填写添加持久卷参数-名称-卷插件-容量-路径-服务器-访问模式
nfs-pv
NFS-Share 10
/water/runfile/docker_volume 192.168.232.140
是否只读：否
多主机读写

点击完成
持久卷PV添加完成后的可用状态-Available


8.2.4 在项目Default中创建数据卷（PVC），pv是全局的，pvc是命名空间的
工作负载-负载均衡-服务发现-PVC(勾)-流水线

添加数据卷-先选择项目-数据卷-添加卷
填写：卷声明名称-选择刚创建的持久化卷-访问模式
nfs-pvc-redis
持久卷(PV) 选择：nfs-pv
自定义： 多主机读写

数据卷的卷声明和持久卷已经绑定完毕

8.2.5部署工作负载（redis）时使用PVC
这步在8.6创建reids服务的时候来选择卷里操作


------------------------
140上执行：
mkdir -p /water/runfile/docker_volume/redis/data

目录挂载
/water/runfile/docker_volume/redis/data:/data


自定义配置文件挂载：
创建配置文件目录
mkdir -p /water/runfile/docker_volume/redis/conf
编辑配置文件
cd /water/runfile/docker_volume/redis/conf
vi redis.conf
,,文件在文件夹下，因为内容太多不贴在这里
相关修改的配置参考
https://blog.csdn.net/JetaimeHQ/article/details/83303346

8.3rancher部署redis服务
redisM
192.168.232.140:5000/redis:5.0.5-alpine3.10
6379 6379
主机调度：redis=redisM
数据卷：挂载到远程主机存储路径，选择"使用现有PVC"
redism-data
选择pvc：nfs-pvc-redis
/data redis/data
/usr/local/etc/redis/redis.conf redis/conf/redis.conf

默认是不用配置文件启动的，在高级选项，命令里，加上redis-server /usr/local/etc/redis/redis.conf
高级选项 网络 选用主机网络

firewall-cmd --zone=public --add-port=6379/tcp --permanent
firewall-cmd --reload

启动

8.4远程连接redis
redis-cli.exe -h 192.168.232.141 -p 6379

-----------------------------------------------------------------------





8.5rancher部署redis集群
https://cloud.tencent.com/developer/article/1432088
https://my.oschina.net/ruoli/blog/2252393

配置文件还是用140上共享的目录

mkdir -p /water/runfile/docker_volume/redis_cluster
mkdir -p /water/runfile/docker_volume/redis_cluster/7001/data
mkdir -p /water/runfile/docker_volume/redis_cluster/7002/data
mkdir -p /water/runfile/docker_volume/redis_cluster/7003/data
cd /water/runfile/docker_volume/redis_cluster

----------------
rpc.nfsd 8
rpc.mountd

exportfs -r
#使配置生效

exportfs
#可以查看到已经ok
----------------

vi /water/runfile/docker_volume/redis_cluster/7001/redis.conf
vi /water/runfile/docker_volume/redis_cluster/7002/redis.conf
vi /water/runfile/docker_volume/redis_cluster/7003/redis.conf


将原版的redis配置写入，并主要修改如下配置：

#bind 127.0.0.1
port 7001  #端口
cluster-enabled yes #启用集群模式
cluster-config-file /data/nodes.conf
cluster-node-timeout 5000 #超时时间
logfile "/data/redis.log" #输出日志
appendonly yes
daemonize no #是否后台运行，这里不能改成yes，不然docker运行就关闭了
protected-mode no #非保护模式
pidfile  /var/run/redis.pid



8.6配置文件完成后开始在rancher里创建redis服务

在rancher里springcloud里添加一个新的命名空间
redis-cluster

保存

8.7先加一个rediscluster命名空间的pv和pvc用来共享存储,开始建的命名空间在Default,所以用不了

填写添加持久卷参数-名称-卷插件-容量-路径-服务器-访问模式
nfs-redisclutser-pv
NFS-Share 10
/water/runfile/docker_volume 192.168.232.140
是否只读：否
多主机读写

点击完成
持久卷PV添加完成后的可用状态-Available


工作负载-负载均衡-服务发现-PVC(勾)-流水线
选择添加pvc
nfs-redisclutser-pvc 命名空间:选择刚创建的redis-cluster
持久卷(PV) 选择：nfs-redisclutser-pv
自定义： 多主机读写

数据卷的卷声明和持久卷已经绑定完毕


8.8部署redis master服务（部署工作负载）
redisClusterM
192.168.232.140:5000/redis:5.0.5-alpine3.10 命名空间:选择刚创建的redis-cluster
7001 7001
17001 17001
主机调度：redis=redisM
数据卷：挂载到远程主机存储路径，选择"使用现有PVC"
redisclusterm-data
选择pvc：nfs-redisclutser-pvc
/data redis_cluster/7001/data
/usr/local/etc/redis/redis.conf redis_cluster/7001/redis.conf

数据卷：将时区文件挂载一下,选择"映射主机目录"
localtime
/etc/localtime /etc/localtime


数据卷：将/usr/bin挂载一下,选择"映射主机目录",不然启动redis集群有问题
usr-bin
/usr/bin /usr/bin
如果这个目录映射不行，就用docker logs命令查看镜像调用的docker-entrypoint.sh文件在哪里,在主机上找出
find / -name docker-entrypoint.sh
cp .....到某个主机目录，映射到容器启动的路径上,如下:
/usr/local/bin/docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh


默认是不用配置文件启动的，在高级选项，命令里，加上redis-server /usr/local/etc/redis/redis.conf
高级选项 网络 选用主机网络

firewall-cmd --zone=public --add-port=7001/tcp --permanent
firewall-cmd --zone=public --add-port=17001/tcp --permanent
firewall-cmd --reload

启动

8.9部署redis slaves（部署工作负载）
redisClusterS1
192.168.232.140:5000/redis:5.0.5-alpine3.10 命名空间:选择刚创建的redis-cluster
7002 7002
17002 17002
主机调度：redis=redisS
数据卷：挂载到远程主机存储路径，选择"使用现有PVC"
redisclusters-data
选择pvc：nfs-redisclutser-pvc
/data redis_cluster/7002/data
/usr/local/etc/redis/redis.conf redis_cluster/7002/redis.conf

数据卷：将时区文件挂载一下,选择"映射主机目录"
localtime
/etc/localtime /etc/localtime


数据卷：将/usr/bin挂载一下,选择"映射主机目录",不然启动redis集群有问题
usr-bin
/usr/bin /usr/bin
如果这个目录映射不行，就用docker logs命令查看镜像调用的docker-entrypoint.sh文件在哪里,在主机上找出
find / -name docker-entrypoint.sh
cp .....到某个主机目录，映射到容器启动的路径上,如下:
/usr/local/bin/docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh


默认是不用配置文件启动的，在高级选项，命令里，加上redis-server /usr/local/etc/redis/redis.conf
高级选项 网络 选用主机网络

firewall-cmd --zone=public --add-port=7002/tcp --permanent
firewall-cmd --zone=public --add-port=17002/tcp --permanent
firewall-cmd --reload

-----------------------------
redisClusterS2
192.168.232.140:5000/redis:5.0.5-alpine3.10 命名空间:选择刚创建的redis-cluster
7003 7003
17003 17003
主机调度：redis=redisS
数据卷：挂载到远程主机存储路径，选择"使用现有PVC"
redisclusters-data
选择pvc：nfs-redisclutser-pvc
/data redis_cluster/7003/data
/usr/local/etc/redis/redis.conf redis_cluster/7003/redis.conf

数据卷：将时区文件挂载一下,选择"映射主机目录"
localtime
/etc/localtime /etc/localtime


数据卷：将/usr/bin挂载一下,选择"映射主机目录",不然启动redis集群有问题
usr-bin
/usr/bin /usr/bin
如果这个目录映射不行，就用docker logs命令查看镜像调用的docker-entrypoint.sh文件在哪里,在主机上找出
find / -name docker-entrypoint.sh
cp .....到某个主机目录，映射到容器启动的路径上,如下:
/usr/local/bin/docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh


默认是不用配置文件启动的，在高级选项，命令里，加上redis-server /usr/local/etc/redis/redis.conf
高级选项 网络 选用主机网络

firewall-cmd --zone=public --add-port=7003/tcp --permanent
firewall-cmd --zone=public --add-port=17003/tcp --permanent
firewall-cmd --reload




8.10将三个redis串联起来变成集群
下载redis-cli客户端镜像，在140上执行:

docker pull goodsmileduck/redis-cli:v5.0.3
docker tag goodsmileduck/redis-cli:v5.0.3 192.168.232.140:5000/goodsmileduck/redis-cli:v5.0.3
docker push 192.168.232.140:5000/goodsmileduck/redis-cli:v5.0.3
docker rmi 192.168.232.140:5000/goodsmileduck/redis-cli:v5.0.3
docker rmi goodsmileduck/redis-cli:v5.0.3

8.11在rancher里部署redis-cli服务
redis-cli
192.168.232.140:5000/goodsmileduck/redis-cli:v5.0.3
网络:是否用主机网络:是

启动

8.12在rancher里执行命令，将三个redis串联起来
cd bin
redis-cli --cluster create 192.168.232.141:7001 192.168.232.142:7002 192.168.232.142:7003 --cluster-replicas 0
输入:yes
#redis集群至少要3个主节点，这里只有3个节点，所以没有从节点，所以cluster-replicas为0


#如果是六个节点的话就执行如下
redis-cli --cluster create 192.168.232.141:7001 192.168.232.142:7002 192.168.232.142:7003 192.168.232.142:7004 192.168.232.142:7005 192.168.232.142:7006 --cluster-replicas 1

create 表示创建一个redis集群。 
--cluster-replicas 1 表示为集群中的每一个主节点指定一个从节点，即一比一的复制


8.13查看redis集群
在windows启动redis client时，要使用集群模式启动，不然操作key因为哈希槽操作而报错不方便
redis-cli.exe -c -h 192.168.232.142 -p 7003

查看集群中的节点
cluster nodes





---------------------------------------------------------------------------------
下面是单机版mysql
----------------------
9.0搭建mysql镜像
docker pull mysql:8.0.17
docker tag mysql:8.0.17 192.168.232.140:5000/mysql:8.0.17
docker push 192.168.232.140:5000/mysql:8.0.17
docker rmi 192.168.232.140:5000/mysql:8.0.17
docker rmi mysql:8.0.17

9.1创建mysql映射的文件夹
mkdir -p /water/runfile/docker_volume/mysql8/data
mkdir -p /water/runfile/docker_volume/mysql8/conf

9.2给141主机创建标签
mysql=mysqlM
给142主机创建标签
mysql=mysqlS

9.2在rancher里配置mysql服务，启动后，在容器内将配置文件给捞出来
mysql8
192.168.232.140:5000/mysql:8.0.17 命名空间：default
3306 3306

增加环境变量
MYSQL_ROOT_PASSWORD=123456

主机调度：mysql=mysqlM
数据卷：挂载到远程主机存储路径，选择"使用现有PVC"
mysqlm-data
选择pvc：nfs-pvc-redis(以前单redis创建的)
/var/lib/mysql mysql8/data
#/etc/mysql/my.cnf mysql8/conf/my.cnf

高级选项 网络：选择主机网络

启动

9.2.1在linux shell获取conf文件内容并将root登录的范围改成不限制ip
docker ps
docker exec -it 47da7da9f81d /bin/bash

修改root登录范围:
mysql -u root -p
123456
use mysql;
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456';
flush privileges;


cd /etc/mysql/
/bin/cat my.cnf
复制内容就出来了

9.2.3将配置文件加上几行配置
[client]
default-character-set=utf8mb4

[mysql]
default-character-set=utf8mb4


在[mysqld]下加上
max_connections=10000
default-time_zone='+8:00'
character-set-client-handshake=FALSE
character_set_server=utf8mb4
collation-server=utf8mb4_unicode_ci
init_connect='SET NAMES utf8mb4 COLLATE utf8mb4_unicode_ci'
wait_timeout=2147483 
interactive_timeout=2147483

停止容器然后将my.conf复制到140的共享存储目录mysql8/conf里面
清除上次启动映射出的文件 rm -rf /water/runfile/docker_volume/mysql8/data/*

firewall-cmd --zone=public --add-port=3306/tcp --permanent
firewall-cmd --reload




9.3在rancher里启动mysql8
然后用navicat连接192.168.232.141 3306
窗口创建lovesound数据库
运行1.oauth2表.sql
---------------------------------------------------------------------------------







10.0Mysql主从搭建
修改mysql的配置文件，增加如下属性
：server-id属性的值需要每个mysql不一样

port=3310

[client]
socket=/var/run/mysqld/mysqld.sock
[mysql]
socket=/var/run/mysqld/mysqld.sock

[mysqld]
skip-host-cache
skip-name-resolve
general_log_file=/var/lib/mysql/query.log
slow_query_log_file=/var/lib/mysql/slow.log
log-error=/var/lib/mysql/error.log
log-bin=mysql-bin
server-id=10000



从的：

port=3311

[client]
socket=/var/run/mysqld/mysqld.sock
[mysql]
socket=/var/run/mysqld/mysqld.sock

[mysqld]
skip-host-cache
skip-name-resolve
general_log_file=/var/lib/mysql/query.log
slow_query_log_file=/var/lib/mysql/slow.log
log-error = /var/lib/mysql/error.log
log-bin=mysql-bin
server-id=10001


firewall-cmd --zone=public --add-port=3310/tcp --permanent
firewall-cmd --reload

firewall-cmd --zone=public --add-port=3311/tcp --permanent
firewall-cmd --reload

10.0.1在rancher创建mysql主从

10.1创建mysql映射的文件夹
mkdir -p /water/runfile/docker_volume/mysql8_cluster/3310/data
mkdir -p /water/runfile/docker_volume/mysql8_cluster/3310/conf

mkdir -p /water/runfile/docker_volume/mysql8_cluster/3311/data
mkdir -p /water/runfile/docker_volume/mysql8_cluster/3311/conf
上传conf文件到对应的conf文件夹里


10.2在rancher里配置主mysql服务
mysql8ClusterM
192.168.232.140:5000/mysql:8.0.17 命名空间：default
#3310 3310
3310 3306

增加环境变量
MYSQL_ROOT_PASSWORD=123456

主机调度：mysql=mysqlM
数据卷：挂载到远程主机存储路径，选择"使用现有PVC"
mysqlm-data
选择pvc：nfs-pvc-redis(以前单redis创建的)
/var/lib/mysql mysql8_cluster/3310/data
/etc/mysql/my.cnf mysql8_cluster/3310/conf/my.cnf

#高级选项 网络：选择主机网络···这里不使用主机网络,因为端口映射问题

启动


10.3在rancher里配置从mysql服务
mysql8ClusterS
192.168.232.140:5000/mysql:8.0.17 命名空间：default
3311 3311

增加环境变量
MYSQL_ROOT_PASSWORD=123456

主机调度：mysql=mysqlS
数据卷：挂载到远程主机存储路径，选择"使用现有PVC"
mysqls-data
选择pvc：nfs-pvc-redis(以前单redis创建的)
/var/lib/mysql mysql8_cluster/3311/data
/etc/mysql/my.cnf mysql8_cluster/3311/conf/my.cnf

高级选项 网络：选择主机网络

启动


10.4在linux shell获取conf文件内容并将root登录的范围改成不限制ip
docker ps
docker exec -it 47da7da9f81d /bin/bash

修改root登录范围:
mysql -u root -p
123456
use mysql;
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456';
flush privileges;


10.5使用数据库连接工具进行连接，先连接主服务器的数据库

依次执行
GRANT REPLICATION SLAVE ON *.* TO 'root'@'%';
flush privileges;
show master status;
可以看到主数据库的状态



10.6使用数据库连接工具切换到从数据库，依次执行
CHANGE MASTER TO
MASTER_HOST='192.168.232.141',
MASTER_PORT=3306,
MASTER_USER='root',
MASTER_PASSWORD='123456',
MASTER_LOG_FILE='mysql-bin.000003',
MASTER_LOG_POS=993;

#这里的MASTER_LOG_FILE和MASTER_LOG_POS是上面show master status命令查出来的

start slave;

show slave status;










11.0创建spring-common配置jenkins里的打包
因为后面的项目开始引用这个公共包了
项目名称springcloud-common
spring-base/spring-common/pom.xml
clean install -Dmaven.test.skip=true

保存 构建

11.1在jenkins创建springcloud-simple的maven项目
springcloud-simple
https://gitee.com/iwave/springcloud-docker-cluster.git
pom.xml
clean install -Dmaven.test.skip=true

点击maven的高级按钮

DEFAULT
Settings file 选择文件系统中的settings文件
/var/jenkins_home/tools/hudson.tasks.Maven_MavenInstallation/jenkins-in-maven/conf/settings.xml





11.2创建spring-oauth的Dockerfile文件并配置jenkins里的打包
项目名称springcloud-oauth
spring-oauth/pom.xml
clean package -Dmaven.test.skip=true

点击maven的高级按钮

DEFAULT
Settings file 选择文件系统中的settings文件
/var/jenkins_home/tools/hudson.tasks.Maven_MavenInstallation/jenkins-in-maven/conf/settings.xml


执行shell->
cp /var/jenkins_home/workspace/springcloud-oauth/spring-oauth/src/main/docker/Dockerfile /var/jenkins_home/workspace/springcloud-oauth/spring-oauth/target/


Directory for Dockerfile->/var/jenkins_home/workspace/springcloud-oauth/spring-oauth/target/

Image->192.168.232.140:5000/spring-oauth
Clean local images勾选，推到私有仓库后本地不留



11.3在rancher里创建oauth服务
在项目中复制docker文件夹及Dockerfile文件
spring-oauth
192.168.232.140:5000/spring-oauth
8002 8002
主机调度 deploy worker
高级选项 网络 是否选用主机网络选择是,后台会在启动时加上--net=host命令,表示不虚拟出网卡,不然erueka注册ip有问题

配置linux本机的端口映射，网络防火墙
firewall-cmd --zone=public --add-port=8002/tcp --permanent
firewall-cmd --reload

oauth部署成功



12.0在jenkins创建txlcn-tm项目和docker打包
先执行项目中的tx-manager.sql脚本

在jenkins里创建复制zuul项目的配置
项目名称springcloud-txlcn-tm
txlcn-tm/pom.xml
clean package -Dmaven.test.skip=true

点击maven的高级按钮

DEFAULT
Settings file 选择文件系统中的settings文件
/var/jenkins_home/tools/hudson.tasks.Maven_MavenInstallation/jenkins-in-maven/conf/settings.xml


执行shell->
cp /var/jenkins_home/workspace/springcloud-txlcn-tm/txlcn-tm/src/main/docker/Dockerfile /var/jenkins_home/workspace/springcloud-txlcn-tm/txlcn-tm/target/



Directory for Dockerfile->/var/jenkins_home/workspace/springcloud-txlcn-tm/txlcn-tm/target/

Image->192.168.232.140:5000/txlcn-tm
Clean local images勾选，推到私有仓库后本地不留


12.1在rancher里创建txlcn-tm服务
txlcn-tm
192.168.232.140:5000/txlcn-tm
7970 7970
8070 8070
主机调度 deploy worker
高级选项 网络 是否选用主机网络选择是,后台会在启动时加上--net=host命令,表示不虚拟出网卡,不然erueka注册ip有问题

配置linux本机的端口映射，网络防火墙
firewall-cmd --zone=public --add-port=7970/tcp --permanent
firewall-cmd --zone=public --add-port=8070/tcp --permanent
firewall-cmd --reload

txlcn-tm部署成功







13.0在jenkins创建springcloud-spring-smallprogram-client项目和docker打包

在jenkins里创建复制zuul项目的配置
项目名称springcloud-spring-smallprogram-client
spring-smallprogram-client/pom.xml
clean package -Dmaven.test.skip=true

点击maven的高级按钮

DEFAULT
Settings file 选择文件系统中的settings文件
/var/jenkins_home/tools/hudson.tasks.Maven_MavenInstallation/jenkins-in-maven/conf/settings.xml


执行shell->
cp /var/jenkins_home/workspace/springcloud-spring-smallprogram-client/spring-smallprogram-client/src/main/docker/Dockerfile /var/jenkins_home/workspace/springcloud-spring-smallprogram-client/spring-smallprogram-client/target/



Directory for Dockerfile->/var/jenkins_home/workspace/springcloud-spring-smallprogram-client/spring-smallprogram-client/target/

Image->192.168.232.140:5000/spring-smallprogram-client
Clean local images勾选，推到私有仓库后本地不留


13.1在rancher里创建spring-smallprogram-client服务
192.168.232.140:5000/spring-smallprogram-client
8005 8005
主机调度 deploy worker
高级选项 网络 是否选用主机网络选择是,后台会在启动时加上--net=host命令,表示不虚拟出网卡,不然erueka注册ip有问题

配置linux本机的端口映射，网络防火墙
firewall-cmd --zone=public --add-port=8005/tcp --permanent
firewall-cmd --reload

spring-smallprogram-client部署成功









https://www.jianshu.com/u/4f36546f7853





日志打印：
docker logs -f -t --tail 200 docker_id

按条件删除镜像
docker rmi --force `docker images | grep 192.168.232.140:5000/spring-zuul | awk '{print $3}'`
docker rmi --force `docker images | grep 192.168.232.140:5000/spring-config | awk '{print $3}'`

按条件删除没有tag的镜像，就是tag的值是none的镜像
docker images|grep none|awk '{print $3}'|xargs docker rmi

进入容器内部
docker attach 44fc0f0582d9 
docker exec -it 44fc0f0582d9 /bin/bash
docker exec -it 44fc0f0582d9 /bin/sh

该指令默认只会清除悬空镜像，未被使用的镜像不会被删除。
docker system prune 
添加-a 或 --all参数后，可以一并清除所有未使用的镜像和悬空镜像
docker system prune -a

docker inspect 44fc0f0582d9

查看私有仓库所有的镜像 curl -XGET http://192.168.232.140:5000/v2/_catalog
curl -XGET http://192.168.232.140:5000/v2/镜像名/tags/list





问题集：
1.删除无法停止的容器
执行删除命令无法删除docker的目录：
ll /var/lib/docker/containers | grep caf8ef20f3c1
cd /var/lib/docker/containers 
rm -rf caf8ef20f3c1c78f03a5844ee23abc1d7e44246f242292040f1ef288899d0cb8


2.安装时间同步插件
https://blog.csdn.net/vah101/article/details/91868147




```

