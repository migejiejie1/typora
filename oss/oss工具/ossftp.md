```basic
ossftp是一个特殊的FTP server，可以将对文件、文件夹的操作映射为对OSS的操作，使您可以基于FTP协议来管理存储在OSS上的文件。

使用限制
ossftp主要提供给个人测试时使用，生产环境建议使用OSS管理控制台、ossutil、ossbrowser、SDK等工具管理您的OSS。
FTP协议是明文传输的，为了防止您的密码泄漏，建议将ossftp和客户端运行在同一台机器上，通过127.0.0.1:port访问。
ossftp服务器在同一时间只允许一台客户端连接，新发起的连接请求会导致已经连接的客户端断开。

部署环境
支持系统：Windows、Linux、macOS
支持架构：x86（32bit、64bit）
运行环境：Python2.7、Python3.x

主要功能
支持文件和文件夹的上传、下载、删除操作。
支持使用分片上传方式上传大文件。
支持大部分FTP指令。

```



