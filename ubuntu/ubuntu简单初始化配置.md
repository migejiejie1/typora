1. openssh-server安装

  ```shell
  apt-get install openssh-server
  cp /etc/ssh/sshd_config /etc/ssh/sshd_config.ori
  systemctl start ssh
  systemctl enable ssh
  ```

2. 允许root用户登录桌面

3. 配置阿里源
    ubuntu 20.04(focal) 配置如下

  ```shell
  # vim /etc/apt/sources.list
  
  deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
  deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
  
  deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
  deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
  
  deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
  deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
  
  deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
  deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
  
  deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
  deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
  
  # apt-get updata
  # apt-get upgrade
  ```

4. 关闭ufw防火墙

  ```shell
  systemctl stop ufw
  systemctl disable ufw
  ```

5. 配置vnc, 远程登录桌面