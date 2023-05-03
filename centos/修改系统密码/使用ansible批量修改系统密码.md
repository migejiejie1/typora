使用ansible批量修改centos/ubuntu系统密码

1.在对应的centos/ubuntu服务器上传ansible服务器的公钥，确保能够免密登录

2.编写密码设置脚本

```shell
# vim pw.sh   #指定密码
#!/bin/bash
echo "root:!G@sip02o22"|chpasswd

#centos and ubuntu通用:   #创建随机密码
Passwd=$(< /dev/urandom tr -dc _A-Z-a-z-0-9^$%+-.?@[] | head -c 24)
IP=$(ip a|awk -F '[ |/]+' '/inet 10.160./ {print $3}')
echo $IP $Passwd 
echo "root:${Passwd}"|chpasswd &> /dev/null ;echo $?
unset Passwd IP
```

3.编写ansible的hosts文件，添加服务器列表

```shell
# vim /etc/ansible/hosts
[servers]
192.168.132.[152:235]:22
10.160.19.61:12306 ansible_ssh_user=root ansible_ssh_private_key_file=/etc/ansible/ssh_key/Cloud-platform
```

4.编写ansible playbook

```shell
# vim main.yaml
- hosts: servers
  remote_user: root
  tasks:
    - name: transfer file to server
      copy: src=/etc/ansible/chpasswd/pw.sh dest=/tmp/pswd.sh mode=755
    - name: run
      become: yes
      shell: /bin/bash -x /tmp/pswd.sh
    - name: remove pswd.sh
      #shell: /bin/rm -f /tmp/pswd.sh
      file: path=/tmp/pswd.sh state=absent
```

5.执行剧本

```shell
# ansible-playbook main.yaml
```

