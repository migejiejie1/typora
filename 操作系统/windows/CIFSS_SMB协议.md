CIFS是一个网络文件共享协议，允许Internet和Intranet中的Windows主机访问网络中的文件或其他资源。

CIFS协议介绍
CIFS是SMB（Server Message Block）的一个公共版本。SMB协议是一个网络文件访问协议，使本机程序可以访问局域网内计算机上的文件并请求此计算机的服务。

存储系统支持SMB 1.0、SMB2（SMB 2.0和SMB 2.1）和SMB 3.0版本的协议。对于不同版本的Windows客户端版本，存储系统能自动匹配不同的协议版本。

如果客户端是Windows Server 2003，Windows XP，则适配为SMB 1.0。
如果客户端是Windows Server 2008，Windows Vista，则适配为SMB 2.0。
如果客户端是Windows Server 2008 R2，Windows 7，则适配为SMB 2.1。
如果客户端是Windows Server 2012，Windows 8，则适配为SMB 3.0。

存储系统默认关闭SMB 1.0服务开关。如果客户端是Windows Server 2003，Windows XP，需要在存储系统侧使用CLI命令change service cifs smb1_enable=yes开启SMB 1.0服务。SMB 1.0协议建议使用Windows客户端访问，不推荐使用Linux客户端访问。您可以通过CLI命令show service cifs查询CIFS服务的各项开关状态。有关CLI命令的详细说明请参考《命令参考》。
由于协议自身机制的限制，存储系统在线升级时不能保证使用SMB1.0文件共享协议的业务不中断。
如果环境中的现有客户端使用的是SMB 1.0协议，您应尽快升级客户端版本，以使用更高版本的SMB协议，获得安全性和合规性增强功能。
存储系统支持CIFS共享特性，使得Windows客户端能够识别并访问网络中存储系统提供的共享资源，客户端用户能够像使用本机一样对保存在存储系统中的文件进行读、写、创建等操作。

Homedir：Homedir是CIFS共享方式的一种，是指把文件系统以私有目录的形式共享给用户。和普通CIFS共享的区别在于用户访问Homedir共享时是访问用户自己的私有目录。Homedir共享可以像普通CIFS共享一样进行创建（包括配置共享权限和共享特性开关）、查询、修改和删除操作。

Homedir共享的使用特点如下：

客户可以根据业务需要划分不同用户的Homedir目录到不同的文件系统路径。
支持单个用户配置多个Homedir共享，用户可以根据配置的Homedir共享名进行访问。
Homedir共享支持普通CIFS共享支持的特性开关，可以对用户访问Homedir业务进行特性控制。
映射规则支持AutoCreate，可以避免管理员为每个用户创建Homedir目录，减轻管理员的运维负担。