```shell
ubuntu删除所有带 rc 标记的dpkg包

dpkg -l 命令可以浏览所有安装的包，其中 rc 状态的包即卸载了包却保留了配置文件。如果想要完整删除所有 rc 状态的包一个一个删还是很麻烦的，所以可以使用以下命令进行清理

dpkg -l | grep ^rc | cut -d' ' -f3 | sudo xargs dpkg --purge


$ dpkg -l
期望状态=未知(u)/安装(i)/删除(r)/清除(p)/保持(h)
| 状态=未安装(n)/已安装(i)/仅存配置(c)/仅解压缩(U)/配置失败(F)/不完全安装(H)/触发器等待(W)/触发器未决(T)
|/ 错误?=(无)/须重装(R) (状态，错误：大写=故障)
||/ 名称                       版本                         体系结构     描述
+++-===========================-===========================-============-==============>
ii  accountsservice             22.07.5-2ubuntu1.3         amd64        query and mani>
ii  acl                         2.3.1-1                    amd64        access control>
ii  acpi-support                0.144                      amd64        scripts for ha>
ii  acpid                       1:2.0.33-1ubuntu1          amd64        Advanced Confi>
ii  adduser                     3.118ubuntu5               all          add and remove>
ii  adwaita-icon-theme          41.0-1ubuntu1              all          default icon t>
```

