```shell
1. 使用minikube开启仪表盘
minikube addons enable dashboard
kubectl apply -f /opt/kubernetes-dashboard.yaml

# cat kubernetes-dashboard.yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: kubernetes-dashboard
  name: kubernetes-dashboard-katacoda
  namespace: kubernetes-dashboard
spec:
  ports:
  - port: 80
    protocol: TCP    
    targetPort: 9090
    nodePort: 30000
  selector:
    k8s-app: kubernetes-dashboard
  type: NodePort

Kubernetes仪表板使您可以在UI中查看应用程序。在此部署中，仪表板已在端口30000上可用，但可能需要一段时间才能启动。

要查看仪表板启动的进度，请使用以下命令查看kube-system命名空间中的Pod
kubectl get pods -n kubernetes-dashboard -w
运行后，仪表板的URL为 https://IP:30000

2. 管理令牌,只要其他节点具有正确的令牌，便可以加入群集。
kubeadm token list
令牌
kubeadm join --discovery-token-unsafe-skip-ca-verification --token=102952.1a7dd4cc8d1f4cc5 172.17.0.57:6443
该--discovery-token-unsafe-skip-ca-verification标签用于绕过发现令牌验证

```

