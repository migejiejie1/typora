```shell
https://www.cnblogs.com/leaderjs/p/13151042.html

目录
一、docker容器的结构
1、 查看containerd的pid
2、 查看 父进程是containerd的进程，全是 containerd-shim
3、 查看一个containerd-shim 和子进程
4、查看这个nginx的容器
二、 pod与容器，cgroup
1、systemctl status && systemd-cgls
2、从目录里看 cgroup
3、只看我这一个pod
三、 pod和容器，namespace
1、查看pause容器的进程号： 19057
2、 查看 redis容器的进程号：19356
3、 查看 nginx容器的进程号：19431
4、 查看 pod里进程们到底共享什么namespace
四、总结pod和容器的关系
一、docker容器的结构
containerd 是老大，新建一个容器会先新建 containerd-shim，containerd-shim 会建出来最终的docker容器。
1、 查看containerd的pid
pidof containerd == 2841
2、 查看 父进程是containerd的进程，全是 containerd-shim
ps -A -ostat,pid,ppid,user,cmd|grep 2841


Ssl   2841     1 root     /usr/bin/containerd
Sl    7320  2841 root     containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/52eeb71ffa22cd8020a6214fa1a556c2e22c3012858a75aa5799b021502916e1 -address /run/containerd/containerd.sock -containerd-binary /usr/bin/containerd -runtime-root /var/run/docker/runtime-runc

Sl    7342  2841 root     containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/08bcf4df1a2072dc69f825517c6c1ace3ed81886d420fe974fc0683ae61aa7fb -address /run/containerd/containerd.sock -containerd-binary /usr/bin/containerd -runtime-root /var/run/docker/runtime-runc

Sl    8087  2841 root     containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/c3d3530cf2f5dddff0d1a37c5cd6791ffb15cf21d5e1096d96ca36269e077136 -address /run/containerd/containerd.sock -containerd-binary /usr/bin/containerd -runtime-root /var/run/docker/runtime-runc

Sl   19039  2841 root     containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/63ee2df4c255110248842e885fb0b9dafca9791dda6a00499bbc3fc99e153743 -address /run/containerd/containerd.sock -containerd-binary /usr/bin/containerd -runtime-root /var/run/docker/runtime-runc

Sl   19338  2841 root     containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/9934ac36efade11bde691c12729a1dfa7483b8864ae4ded7a77a03f3b6e84d94 -address /run/containerd/containerd.sock -containerd-binary /usr/bin/containerd -runtime-root /var/run/docker/runtime-runc

Sl   19407  2841 root     containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/d62b4a22abd21d47636a1c84e970ad7e76d8fe633232715f74606ee93c71291a -address /run/containerd/containerd.sock -containerd-binary /usr/bin/containerd -runtime-root /var/run/docker/runtime-runc
3、 查看一个containerd-shim 和子进程
# 这是一个 nginx容器
ps -A -opid,ppid,user,cmd |grep 7320

 7320  2841  root     containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/52eeb71ffa22cd8020a6214fa1a556c2e22c3012858a75aa5799b021502916e1 -address /run/containerd/containerd.sock -containerd-binary /usr/bin/containerd -runtime-root /var/run/docker/runtime-runc

 7337  7320  root     nginx: master process nginx -g daemon off;
4、查看这个nginx的容器
# docker ps |grep 52eeb71ffa
52eeb71ffa22        xxxx/xxxx/safe-nginx            "nginx -g 'daemon of…"   41 hours ago        Up 41 hours                             k8s_safe-nginx_668c459d6-pp7bxxx-xxxx_4e11582d-aeea-11ea-8af4-0050569e47b9_0
二、 pod与容器，cgroup
1、systemctl status && systemd-cgls
在不加最后的unit参数的时候，这个命名变得很陌生了。主要是查看 cgroup状态。
[root@my-node1 ~]# systemctl status
● my-node1
    State: running
     Jobs: 0 queued
   Failed: 0 units
    Since: 一 2020-06-15 16:48:44 CST; 1 day 17h ago
   CGroup: /
           ├─1 /usr/lib/systemd/systemd --switched-root --system --deserialize 21
           ├─kubepods
           │ ├─besteffort
           │ │ └─podf86494f1-aeea-11ea-8af4-0050569e47b9
           │ │   ├─167e38f31f6f225fe7e53a6fc31a1aebb85628531b6b3b438a3591fffefca93c
           │ │   │ └─kube-proxy
           │ │   │   └─6227 /usr/local/bin/kube-proxy --config=/var/lib/kube-proxy/config.conf --hostname-override=my-node1
           │ │   └─1ff0ae01463af951a16ac8e7006ec99333e8a42578118791529755b5704de4cb
           │ │     └─5982 /pause
           │ └─burstable
           │   ├─podf5f16f15-af9e-11ea-8af4-0050569e47b9
           │   │ ├─d62b4a22abd21d47636a1c84e970ad7e76d8fe633232715f74606ee93c71291a
           │   │ │ ├─19431 nginx: master process nginx -g daemon off
           │   │ │ ├─19453 nginx: worker proces
           │   │ │ ├─19454 nginx: worker proces
           │   │ │ ├─19455 nginx: worker proces
           │   │ │ ├─19456 nginx: worker proces
           │   │ │ ├─19457 nginx: worker proces
           │   │ │ ├─19458 nginx: worker proces
           │   │ │ ├─19459 nginx: worker proces
           │   │ │ └─19460 nginx: worker proces
           │   │ ├─9934ac36efade11bde691c12729a1dfa7483b8864ae4ded7a77a03f3b6e84d94
           │   │ │ └─19356 redis-server
           │   │ └─63ee2df4c255110248842e885fb0b9dafca9791dda6a00499bbc3fc99e153743
           │   │   └─19057 /pause
          │   ├─pod4e11582d-aeea-11ea-8af4-0050569e47b9
           │   │ ├─52eeb71ffa22cd8020a6214fa1a556c2e22c3012858a75aa5799b021502916e1
           │   │ │ ├─7337 nginx: master process nginx -g daemon off
           │   │ │ ├─7380 nginx: worker proces
           │   │ │ ├─7381 nginx: worker proces
           │   │ │ ├─7382 nginx: worker proces
           │   │ │ ├─7383 nginx: worker proces
           │   │ │ ├─7384 nginx: worker proces
           │   │ │ ├─7385 nginx: worker proces
           │   │ │ ├─7386 nginx: worker proces
           │   │ │ └─7387 nginx: worker proces
           │   │ └─0a5529d9b0fcb544630ea2722c8a82feaa8c3d2efd7ea4118bc5713ffa604437
           │   │   └─7175 /pause
           │   ├─podf8697bea-aeea-11ea-8af4-0050569e47b9
           │   │ ├─99e0fbfa76ad0141bce359555fa343380a0c27b8a441609b9fe41beed954eda4
           │   │ │ ├─6962 /bin/sh /install-cni.sh
           │   │ │ └─7233 sleep 3600
           │   │ ├─1ac5b03e0a9683313a409330a0c7390ea908d963ec9955ff71d2739882924c2d
           │   │ │ └─6593 /opt/bin/flanneld --ip-masq --kube-subnet-mgr
           │   │ └─dcbc2486a119dac68cb6bb2b90941411927c27a0a58c9027475026d02b83e224
           │   │   └─5967 /pause
           │   └─podf8662a28-aeea-11ea-8af4-0050569e47b9
           │     ├─599b533be6646195bc24f5d32bf2551a131a207fd469522608fe7916b187c7cc
           │     │ └─7049 ./kube-rbac-proxy --logtostderr --secure-listen-address=11.11.176.68:9100 --tls-cipher-suites=TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,TLS_RSA_
           │     ├─a18a4606e095579037d4e7f10276b64ab020abf76472598c39acfa67cd16b0f2
           │     │ └─6715 /bin/node_exporter --web.listen-address=127.0.0.1:9100 --path.procfs=/host/proc --path.sysfs=/host/sys --path.rootfs=/host/root --collector.textfile.directory=/host/key --collector
           │     └─aa8c844c2907348c09244c240927964494ab1a43181c1e2cd1e8154e4451bb78
           │       └─5974 /pause
           ├─user.slice
           │ └─user-0.slice
           │   ├─session-290.scope
           │   │ ├─11908 systemctl status
           │   │ ├─11909 less
           │   │ ├─31551 sshd: root@pts/1
           │   │ └─31579 -bash
           │   ├─session-175.scope
           │   │ ├─30428 sshd: root@pts/0
           │   │ └─30430 -bash
           │   └─session-1.scope
           │     ├─1348 login -- root
           │     ├─1352 -bash
           │     └─1448 bash
           └─system.slice
             ├─rpc-statd.service
             │ └─7519 /usr/sbin/rpc.statd
             ├─kubelet.service
             │ └─5773 /usr/local/sbin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --pod-manifest-path=/etc/kubernetes/manifests --allow-pr
             ├─docker.service
             │ └─2842 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
             ├─containerd.service
             │ ├─ 2841 /usr/bin/containerd
             │ ├─ 5914 containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/dcbc2486a119dac68cb6bb2b90941411927c27a0a58c9027475026d02b83e224 -address /run/contain
             │ ├─ 5918 containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/aa8c844c2907348c09244c240927964494ab1a43181c1e2cd1e8154e4451bb78 -address /run/contain
             │ ├─ 5929 containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/1ff0ae01463af951a16ac8e7006ec99333e8a42578118791529755b5704de4cb -address /run/contain
             │ ├─ 6210 containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/167e38f31f6f225fe7e53a6fc31a1aebb85628531b6b3b438a3591fffefca93c -address /run/contain
             │ ├─ 6575 containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/1ac5b03e0a9683313a409330a0c7390ea908d963ec9955ff71d2739882924c2d -address /run/contain
             │ ├─ 6698 containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/a18a4606e095579037d4e7f10276b64ab020abf76472598c39acfa67cd16b0f2 -address /run/contain
             │ ├─ 6944 containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/99e0fbfa76ad0141bce359555fa343380a0c27b8a441609b9fe41beed954eda4 -address /run/contain
             │ ├─ 7031 containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/599b533be6646195bc24f5d32bf2551a131a207fd469522608fe7916b187c7cc -address /run/contain
             │ ├─ 7156 containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/0a5529d9b0fcb544630ea2722c8a82feaa8c3d2efd7ea4118bc5713ffa604437 -address /run/contain
             │ ├─ 7320 containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/52eeb71ffa22cd8020a6214fa1a556c2e22c3012858a75aa5799b021502916e1 -address /run/contain
             │ ├─ 7342 containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/08bcf4df1a2072dc69f825517c6c1ace3ed81886d420fe974fc0683ae61aa7fb -address /run/contain
             │ ├─ 8087 containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/c3d3530cf2f5dddff0d1a37c5cd6791ffb15cf21d5e1096d96ca36269e077136 -address /run/contain
             │ ├─19039 containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/63ee2df4c255110248842e885fb0b9dafca9791dda6a00499bbc3fc99e153743 -address /run/contain
             │ ├─19338 containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/9934ac36efade11bde691c12729a1dfa7483b8864ae4ded7a77a03f3b6e84d94 -address /run/contain
             │ └─19407 containerd-shim -namespace moby -workdir /var/lib/containerd/io.containerd.runtime.v1.linux/moby/d62b4a22abd21d47636a1c84e970ad7e76d8fe633232715f74606ee93c71291a -address /run/contain
             ├─nkucsd.service
             │ └─1042 nkucsd
             ├─sshd.service
             │ └─1025 /usr/sbin/sshd -D
             ├─postfix.service
             │ ├─1272 /usr/libexec/postfix/master -w
             │ ├─1274 qmgr -l -t unix -u
             │ └─5409 pickup -l -t unix -u
             ├─tuned.service
             │ └─1023 /usr/bin/python -Es /usr/sbin/tuned -l -P
             ├─crond.service
             │ └─731 /usr/sbin/crond -n
             ├─NetworkManager.service
             │ └─717 /usr/sbin/NetworkManager --no-daemon
             ├─vmtoolsd.service
             │ └─716 /usr/bin/vmtoolsd
             ├─vgauthd.service
             │ └─715 /usr/bin/VGAuthService -s
             ├─rsyslog.service
             │ └─710 /usr/sbin/rsyslogd -n
             ├─gssproxy.service
             │ └─719 /usr/sbin/gssproxy -D
             ├─polkit.service
             │ └─707 /usr/lib/polkit-1/polkitd --no-debug
             ├─chronyd.service
             │ └─713 /usr/sbin/chronyd
             ├─dbus.service
             │ └─700 /bin/dbus-daemon --system --address=systemd: --nofork --nopidfile --systemd-activation
             ├─irqbalance.service
             │ └─699 /usr/sbin/irqbalance --foreground
             ├─systemd-logind.service
             │ └─697 /usr/lib/systemd/systemd-logind
             ├─rpcbind.service
             │ └─703 /sbin/rpcbind -w
             ├─auditd.service
             │ └─673 /sbin/auditd
             ├─systemd-udevd.service
             │ └─541 /usr/lib/systemd/systemd-udevd
             ├─lvm2-lvmetad.service
             │ └─531 /usr/sbin/lvmetad -f
             └─systemd-journald.service
               └─513 /usr/lib/systemd/systemd-journald
2、从目录里看 cgroup
# ll /sys/fs/cgroup
总用量 0
drwxr-xr-x 6 root root  0 4月  25 2019 blkio
lrwxrwxrwx 1 root root 11 4月  25 2019 cpu -> cpu,cpuacct
lrwxrwxrwx 1 root root 11 4月  25 2019 cpuacct -> cpu,cpuacct
drwxr-xr-x 6 root root  0 4月  25 2019 cpu,cpuacct
drwxr-xr-x 4 root root  0 4月  25 2019 cpuset
drwxr-xr-x 6 root root  0 4月  25 2019 devices
drwxr-xr-x 4 root root  0 4月  25 2019 freezer
drwxr-xr-x 4 root root  0 4月  25 2019 hugetlb
drwxr-xr-x 6 root root  0 4月  25 2019 memory
lrwxrwxrwx 1 root root 16 4月  25 2019 net_cls -> net_cls,net_prio
drwxr-xr-x 4 root root  0 4月  25 2019 net_cls,net_prio
lrwxrwxrwx 1 root root 16 4月  25 2019 net_prio -> net_cls,net_prio
drwxr-xr-x 4 root root  0 4月  25 2019 perf_event
drwxr-xr-x 4 root root  0 4月  25 2019 pids
drwxr-xr-x 6 root root  0 4月  25 2019 systemd
## 在 /sys/fs/cgroup/systemd 这个目录就是 systemd-cgls 展示的根。

3、只看我这一个pod
我起了一个 pod 里边有一个nginx和一个redis，还有一个pause。
   CGroup: /
           ├─1 /usr/lib/systemd/systemd --switched-root --system --deserialize 21
           ├─kubepods      ### pod的根cgroup
           │ └─burstable     ### pod根据request和limit分为3个保证稳定性的QoS服务质量级别：BestEffort，Burstable，Guaranteed；参考：https://blog.csdn.net/horsefoot/article/details/52091077
           │   ├─podf5f16f15-af9e-11ea-8af4-0050569e47b9          ### 对应于 /var/lib/kubelet/pods/xxxx ，其下的目录： containers  etc-hosts  plugins  volumes
           │   │ ├─d62b4a22abd21d47636a1c84e970ad7e76d8fe633232715f74606ee93c71291a      ### nginx 容器的id
           │   │ │ ├─19431 nginx: master process nginx -g daemon off
           │   │ │ ├─19453 nginx: worker proces
           │   │ │ ├─19454 nginx: worker proces
           │   │ │ ├─19455 nginx: worker proces
           │   │ │ ├─19456 nginx: worker proces
           │   │ │ ├─19457 nginx: worker proces
           │   │ │ ├─19458 nginx: worker proces
           │   │ │ ├─19459 nginx: worker proces
           │   │ │ └─19460 nginx: worker proces
           │   │ ├─9934ac36efade11bde691c12729a1dfa7483b8864ae4ded7a77a03f3b6e84d94    ### redis 容器的id
           │   │ │ └─19356 redis-server
           │   │ └─63ee2df4c255110248842e885fb0b9dafca9791dda6a00499bbc3fc99e153743    ### pause 容器的id
           │   │   └─19057 /pause

# cat /proc/`pidof nginx`/cgroup
11:memory:/kubepods/burstable/podf5f16f15-af9e-11ea-8af4-0050569e47b9/d62b4a22abd21d47636a1c84e970ad7e76d8fe633232715f74606ee93c71291a
10:cpuset:/kubepods/burstable/podf5f16f15-af9e-11ea-8af4-0050569e47b9/d62b4a22abd21d47636a1c84e970ad7e76d8fe633232715f74606ee93c71291a
9:devices:/kubepods/burstable/podf5f16f15-af9e-11ea-8af4-0050569e47b9/d62b4a22abd21d47636a1c84e970ad7e76d8fe633232715f74606ee93c71291a
8:blkio:/kubepods/burstable/podf5f16f15-af9e-11ea-8af4-0050569e47b9/d62b4a22abd21d47636a1c84e970ad7e76d8fe633232715f74606ee93c71291a
7:net_prio,net_cls:/kubepods/burstable/podf5f16f15-af9e-11ea-8af4-0050569e47b9/d62b4a22abd21d47636a1c84e970ad7e76d8fe633232715f74606ee93c71291a
6:perf_event:/kubepods/burstable/podf5f16f15-af9e-11ea-8af4-0050569e47b9/d62b4a22abd21d47636a1c84e970ad7e76d8fe633232715f74606ee93c71291a
5:pids:/kubepods/burstable/podf5f16f15-af9e-11ea-8af4-0050569e47b9/d62b4a22abd21d47636a1c84e970ad7e76d8fe633232715f74606ee93c71291a
4:cpuacct,cpu:/kubepods/burstable/podf5f16f15-af9e-11ea-8af4-0050569e47b9/d62b4a22abd21d47636a1c84e970ad7e76d8fe633232715f74606ee93c71291a
3:hugetlb:/kubepods/burstable/podf5f16f15-af9e-11ea-8af4-0050569e47b9/d62b4a22abd21d47636a1c84e970ad7e76d8fe633232715f74606ee93c71291a
2:freezer:/kubepods/burstable/podf5f16f15-af9e-11ea-8af4-0050569e47b9/d62b4a22abd21d47636a1c84e970ad7e76d8fe633232715f74606ee93c71291a
1:name=systemd:/kubepods/burstable/podf5f16f15-af9e-11ea-8af4-0050569e47b9/d62b4a22abd21d47636a1c84e970ad7e76d8fe633232715f74606ee93c71291a
# cat /proc/`pidof redis-server`/cgroup# cat /proc/19356/cgroup
11:memory:/kubepods/burstable/podf5f16f15-af9e-11ea-8af4-0050569e47b9/9934ac36efade11bde691c12729a1dfa7483b8864ae4ded7a77a03f3b6e84d94
10:cpuset:/kubepods/burstable/podf5f16f15-af9e-11ea-8af4-0050569e47b9/9934ac36efade11bde691c12729a1dfa7483b8864ae4ded7a77a03f3b6e84d94
9:devices:/kubepods/burstable/podf5f16f15-af9e-11ea-8af4-0050569e47b9/9934ac36efade11bde691c12729a1dfa7483b8864ae4ded7a77a03f3b6e84d94
8:blkio:/kubepods/burstable/podf5f16f15-af9e-11ea-8af4-0050569e47b9/9934ac36efade11bde691c12729a1dfa7483b8864ae4ded7a77a03f3b6e84d94
7:net_prio,net_cls:/kubepods/burstable/podf5f16f15-af9e-11ea-8af4-0050569e47b9/9934ac36efade11bde691c12729a1dfa7483b8864ae4ded7a77a03f3b6e84d94
6:perf_event:/kubepods/burstable/podf5f16f15-af9e-11ea-8af4-0050569e47b9/9934ac36efade11bde691c12729a1dfa7483b8864ae4ded7a77a03f3b6e84d94
5:pids:/kubepods/burstable/podf5f16f15-af9e-11ea-8af4-0050569e47b9/9934ac36efade11bde691c12729a1dfa7483b8864ae4ded7a77a03f3b6e84d94
4:cpuacct,cpu:/kubepods/burstable/podf5f16f15-af9e-11ea-8af4-0050569e47b9/9934ac36efade11bde691c12729a1dfa7483b8864ae4ded7a77a03f3b6e84d94
3:hugetlb:/kubepods/burstable/podf5f16f15-af9e-11ea-8af4-0050569e47b9/9934ac36efade11bde691c12729a1dfa7483b8864ae4ded7a77a03f3b6e84d94
2:freezer:/kubepods/burstable/podf5f16f15-af9e-11ea-8af4-0050569e47b9/9934ac36efade11bde691c12729a1dfa7483b8864ae4ded7a77a03f3b6e84d94
1:name=systemd:/kubepods/burstable/podf5f16f15-af9e-11ea-8af4-0050569e47b9/9934ac36efade11bde691c12729a1dfa7483b8864ae4ded7a77a03f3b6e84d94
三、 pod和容器，namespace
就是pause和容器，查看pause进程和容器进程的namespace关系
首先要从容器 id 获得其进程在宿主机上的进程号：
1、查看pause容器的进程号： 19057
# docker top  63ee2df4

UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                19057            19039               0                   Jun16               ?                   00:00:00            /pause
2、 查看 redis容器的进程号：19356
# docker top 9934ac36
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
100                 19356            19338               0                   Jun16               ?                   00:03:21            redis-server


3、 查看 nginx容器的进程号：19431
# docker top d62b4a2
UID                 PID                   PPID                C                   STIME               TTY                 TIME                CMD
root                19431               19407               0                   Jun16               ?                   00:00:00            nginx: master process nginx -g daemon off;
100                 19453               19431               0                   Jun16               ?                   00:00:00            nginx: worker process
100                 19454               19431               0                   Jun16               ?                   00:00:00            nginx: worker process
100                 19455               19431               0                   Jun16               ?                   00:00:00            nginx: worker process
100                 19456               19431               0                   Jun16               ?                   00:00:00            nginx: worker process
100                 19457               19431               0                   Jun16               ?                   00:00:00            nginx: worker process
100                 19458               19431               0                   Jun16               ?                   00:00:00            nginx: worker process
100                 19459               19431               0                   Jun16               ?                   00:00:00            nginx: worker process
100                 19460               19431               0                   Jun16               ?                   00:00:00            nginx: worker process
4、 查看 pod里进程们到底共享什么namespace
## 查看 pause进程的 namespace信息#  ll /proc/19057/ns
总用量 0
lrwxrwxrwx 1 root root 0 6月  16 14:59 ipc -> ipc:[4026532659]  ## same ；ipc namespace
lrwxrwxrwx 1 root root 0 6月  17 10:28 mnt -> mnt:[4026532657] 
lrwxrwxrwx 1 root root 0 6月  16 14:59 net -> net:[4026532662] ## same ；net namespace
lrwxrwxrwx 1 root root 0 6月  17 10:28 pid -> pid:[4026532660]
lrwxrwxrwx 1 root root 0 6月  17 10:28 user -> user:[4026531837] ## same ；user namespace
lrwxrwxrwx 1 root root 0 6月  17 10:28 uts -> uts:[4026532658]
## 查看 redis进程的 namespace信息# ll /proc/19356/ns
总用量 0
lrwxrwxrwx 1 100 101 0 6月  16 15:13 ipc -> ipc:[4026532659]  ## same
lrwxrwxrwx 1 100 101 0 6月  16 15:13 mnt -> mnt:[4026532654]
lrwxrwxrwx 1 100 101 0 6月  16 15:13 net -> net:[4026532662]  ## same
lrwxrwxrwx 1 100 101 0 6月  16 15:13 pid -> pid:[4026532656]
lrwxrwxrwx 1 100 101 0 6月  16 15:13 user -> user:[4026531837]  ## same
lrwxrwxrwx 1 100 101 0 6月  16 15:13 uts -> uts:[4026532655]
## 查看 nginx进程的 namespace信息# ll /proc/19431/ns
总用量 0
lrwxrwxrwx 1 root root 0 6月  16 15:13 ipc -> ipc:[4026532659]  ## same
lrwxrwxrwx 1 root root 0 6月  16 15:13 mnt -> mnt:[4026532849]
lrwxrwxrwx 1 root root 0 6月  16 15:13 net -> net:[4026532662]  ## same
lrwxrwxrwx 1 root root 0 6月  16 15:13 pid -> pid:[4026532851]
lrwxrwxrwx 1 root root 0 6月  16 15:13 user -> user:[4026531837]  ## same
lrwxrwxrwx 1 root root 0 6月  16 15:13 uts -> uts:[4026532850]
四、总结pod和容器的关系
pod是k8s抽象出来的资源类型，是k8s调度的最小单位。这是事实，但是为什么会有pod呢？直接用容器不好吗？
-- 不好！首先容器里只启动一个进程这基本是共识了，那联系紧密的几个进程怎么办，pod维持多个容器紧密联系，他们共享ipc，net和user namespace，他们属于同一组 cgroup，作为一个整体来参与调度；
-- 二、容器技术不只是containerd 这一种引擎，还有 rkt，cri-o等，k8s需要pod这一层更高级的抽象。
标签: linux, kubernetes, k8s, docker

```

