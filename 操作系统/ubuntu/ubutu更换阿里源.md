```shell
Ubuntu更换国内源
1.备份原来的源
sudo cp /etc/apt/sources.list{,.bak}
将以前的源备份一下，以防以后可以用的。

2.更换源

 vi /etc/apt/sources.list

使用gedit或者vim打开文档，将下边的阿里源复制进去，然后点击保存关闭。
阿里源
deb http://mirrors.aliyun.com/ubuntu/ disco main restricted universe multiverse

deb-src http://mirrors.aliyun.com/ubuntu/ disco main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ disco-security main restricted universe multiverse

deb-src http://mirrors.aliyun.com/ubuntu/ disco-security main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ disco-updates main restricted universe multiverse

deb-src http://mirrors.aliyun.com/ubuntu/ disco-updates main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ disco-backports main restricted universe multiverse

deb-src http://mirrors.aliyun.com/ubuntu/ disco-backports main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ disco-proposed main restricted universe multiverse

deb-src http://mirrors.aliyun.com/ubuntu/ disco-proposed main restricted universe multiverse

注意：
有个disco，这是个系统代号，不同的系统代号是不同的，disco是ubuntu19.04版本的代号，比如ubuntu18.04的系统代号是bionic
可以通过命令lsb_release -c查看系统代号

3.
cd /etc/apt/sources.list.d/
sudo mv webupd8team-ubuntu-atom-disco.list  webupd8team-ubuntu-atom-disco.list.bak

4.更新
更新源

sudo apt-get update

更新软件

sudo apt-get upgrade
```

