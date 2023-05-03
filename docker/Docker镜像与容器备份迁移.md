## Docker镜像与容器备份迁移（export、import与commit、save、load）

### 容器与镜像迁移

### **注：**

 **用户既可以使用 docker load 来导入镜像存储文件到本地镜像库，也可以使用 docker import 来导入一个**[**容器**](https://cloud.tencent.com/product/tke?from=10680)**快照到本地镜像库。这两者的区别在于容器快照文件将丢弃所有的历史记录和元数据信息（即仅保存容器当时的快照状态），而镜像存储文件将保存完整记录，体积也要大。此外，从容器快照文件导入时可以重新指定标签等元数据信息。**

### **export与import命令：**

**`注意：`**

 **1.会丢弃历史记录和元数据。   2.启动export与import命令导出导入的镜像必须加/bin/bash或者其他/bin/sh，否则会报错。** `docker: Error response from daemon: No command specified.`

**`export：`导出容器会丢失历史记录和元数据，类似与快照。**

**命令格式：**

 `docker export [容器ID|Name] > xxx.tar` 或 `docker export -o xxx.tar [容器ID|Name]`

**应用场景：**  主要用来制作基础镜像，比如从一个ubuntu镜像启动一个容器，然后安装一些软件和进行一些设置后，使用docker export保存为一个基础镜像。然后，把这个镜像分发给其他人使用，比如作为基础的开发环境。

------

**`import：`导入容器快照到本地镜像库。**

**命令格式：** `docker import xxx.tar newname:tag`

 如：   docker import redis.tar myredis:v1

**1.创建容器web并新增数据**

```shell
[root@localhost ~]# docker run -itd --name web nginx
9a17f7c9f00a3711018581a1523ecd7a06c40d1408ae5678e034be1a1e4e0cd8

[root@localhost ~]# docker exec -it web touch /mnt/test.txt
[root@localhost ~]# docker exec -it web ls /mnt/
test.txt
```

复制

**2.导出容器快照**

```shell
[root@localhost ~]# docker export web > web.tar

[root@localhost ~]# ll -h web.tar
-rw-r--r-- 1 root root 123M 5月  13 04:34 web.tar
```

复制

**3.导入容器快照到本机镜像库**

```shell
[root@localhost ~]# docker import web.tar web:v1
sha256:134f9251e15e56060d564c23cec4be0048434fb90b19188ea64bf77af77b85ff

[root@localhost ~]# docker images
REPOSITORY                      TAG                      IMAGE ID            CREATED             SIZE
web                             v1                       134f9251e15e        10 minutes ago      125MB
```

复制

**4.启动使用import导入镜像库的web:v1镜像，并查看数据**

**`注意：`** **启动export与import命令导出导入的镜像必须加/bin/bash或者其他/bin/sh，否则会报错。** `docker: Error response from daemon: No command specified.`

```shell
[root@localhost ~]# docker run -itd --name web2 web:v1 /bin/bash
ef07135bcda92c8660392ce29e24c7c8de82f3369fb024ae772c34cd74b9258d

[root@localhost ~]# docker exec -it web2 ls /mnt/
test.txt
```

复制

**总结：**  通过export命令也可以将容器里的数据保存，并可以迁移到别的docker主机。

## **commit命令：**

 **将已存在容器中的镜像和修改内容提交为一个新的镜像，通过这个方式同样能保存读写层内容。**

**命令格式：**

 `docker commit [容器名称|ID] 生成新的镜像名字`

**选项说明：**

 `-a：提交的镜像作者`

 `-c：使用dockerfile指令来创建镜像`

 `-m：提交时的说明文字`

 `-p：在commit的时候，将正在运行的容器暂停`

**应用场景：** 主要作用是将配置好的一些容器生成新的镜像，可以得到复用（再次使用不需要再配置）。

**1.启动一个nginx容器，并且在容器的/mnt目录下创建一个文件**

```shell
[root@localhost ~]# docker run -itd --name nginxweb -p 80:80 nginx
fda3ca1b33ba4c9dc6a1ca27c7242bbbdc2a08f4e6e7642d3ec5de62e1e8f78c

[root@localhost ~]# docker exec -it nginxweb touch /mnt/test.txt

[root@localhost ~]# docker exec -it nginxweb ls /mnt/
test.txt
```

复制

**2.将nginxweb容器commit成一个新的镜像**

```shell
[root@localhost ~]# docker commit nginxweb nginx_test:v1
sha256:a06b16b343036bcbf424c499022ca635bf90740aa7d76acbe0c271a731aba2ef

[root@localhost ~]# docker images
REPOSITORY                      TAG                      IMAGE ID            CREATED             SIZE
nginx_test                      v1                       a06b16b34303        4 seconds ago       127MB
```

复制

**3.使用nginxweb生成的新镜像nginx_test启动一个nginx_v1容器，并查看新容器中的数据**

```shell
[root@localhost ~]# docker stop nginxweb     //为了方便先停止nginxweb容器，因为80端口已被占用
nginxweb

[root@localhost ~]# docker run -itd --name nginx_v1 -p 80:80 nginx_test:v1       
df074341d7a39b072966672ef9bb8769142b67395488e30e81711d0c75f2a821

[root@localhost ~]# docker exec -it nginx_v1 ls /mnt/
test.txt       ---》可以看到新容器nginx_v1中有之前nginxweb的数据
```

复制

**`注意：`**  **commit 命令虽然能实现保存读写层数据，但不适于做数据持久化。**

## **save与load命令：**

**`注意：`**  **1.不会丢弃历史记录和元数据，并可以回滚版本。  2.启动不用加/bin/bash。**

**save：将指定镜像保存成tar文件。**

**命令格式：**

 `docker save 镜像名 > xxx.tar` 或 `docker save -o xxx.tar 镜像名`

**应用场景：**  如果你的应用是使用docker-compose.yml编排的多个镜像组合，但你要部署的客户[服务器](https://cloud.tencent.com/product/cvm?from=10680)并不能连外网。这时，你可以使用docker save将用到的镜像打个包，然后拷贝到客户服务器上使用docker load载入（一般用于镜像迁移到别处）。

------

**`load：`导入使用docker save命令导出的镜像。**`在这里插入代码片`

**命令格式：** `docker load -i xxx.tar` 或 `docker load < xxx.tar`

## **容器备份迁移案例：**

 **`运行一段时间后的容器，其中包含了新的数据，如果想把这些内容数据一并迁移到新的主机上，可以按照以下步骤进行：`**

**1.提交容器生成新的镜像**

```shell
[root@localhost ~]# docker ps    //查看正在运行的容器web
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
9a17f7c9f00a        nginx               "nginx -g 'daemon of…"   19 hours ago        Up 19 hours         80/tcp              web

[root@localhost ~]# docker commit -p web webdata:v1    //-p暂停web容器并提交为新镜像webdata：v1
sha256:b25ea02c5f1f4efe4c35d6503a277d968d5dfdf0cfd69092b3e99202dd687723

[root@localhost ~]# docker images      //查看提交的新镜像webdata
REPOSITORY                      TAG                      IMAGE ID            CREATED             SIZE
webdata                         v1                       b25ea02c5f1f        3 seconds ago       127MB
```

复制

**2.将镜像保存成一个tar压缩包**

```shell
[root@localhost ~]# docker save webdata:v1 > webdata.tar

[root@localhost ~]# ll -h webdata.tar
-rw-r--r-- 1 root root 125M 5月  13 23:47 webdata.tar
```

复制

**3.将tar压缩包复制到另一台主机**

```shell
[root@localhost ~]# scp webdata.tar root@192.168.2.128:/root/test
```

复制

**4.在另一台主机上加载镜像的tar压缩包**

```shell
[root@localhost ~]# cd test/

[root@localhost test]# ll
总用量 127572
-rw-r--r-- 1 root root 130631168 5月  13 23:49 webdata.tar

[root@localhost test]# docker load -i webdata.tar
d9d778e6751c: Loading layer [==================================================>]  10.24kB/10.24kB
Loaded image: webdata:v1

[root@localhost test]# docker images
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
webdata                       v1                  b25ea02c5f1f        12 minutes ago      127MB
```

复制

**5.使用这个加载的镜像运行容器**

```shell
[root@localhost test]# docker run -itd --name web webdata:v1
51d9ed10961b9620ea6456f5bd75dbd43168b73c7bd184dcccdd25fcf956d9e5

[root@localhost test]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
51d9ed10961b        webdata:v1          "nginx -g 'daemon of…"   35 seconds ago      Up 33 seconds       80/tcp              web

[root@localhost test]# docker exec -it web ls /mnt/
test.txt
```

复制

**`注意：`** **如果有docker**[**镜像仓库**](https://cloud.tencent.com/product/tcr?from=10680)**的权限，也可以直接将第1步生成的镜像push到docker仓库，然后在另一台主机上pull镜像并运行为容器即可。**

------

## **docker save和docker export的区别**

 **`docker save`保存的是镜像（image）,docker export保存的是容器（container)；**

 **`docker load`用来载入镜像包，docker import用来载入容器包，但两者都会恢复为镜像；**

 **`docker load`不能对载入的镜像重命名，而docker import可以为镜像指定新名称。**

 **`docker export`的包会比save的包要小，原因是save的是一个分层的文件系统，export导出的只是一个linux系统的文件目录。**