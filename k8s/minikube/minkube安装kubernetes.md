```shell
1. 安装docker引擎
2. 安装kubectl
    1)在Linux上使用curl安装Kubectl
        curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
    2)使kubectl二进制可执行文件。
        chmod +x ./kubectl
    3)将二进制文件移到您的PATH中。
        mv ./kubectl /usr/local/bin/kubectl
    4)测试以确保您安装的版本是最新的：
        kubectl version
3. 安装minikube
    curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 \
  && chmod +x minikube  
  && mv minikube /usr/local/bin/minikube
4. 启动k8s集群
    minikube start --vm-driver=none --image-repository=registry.cn-hangzhou.aliyuncs.com/google_containers

```

