Log4j2 漏洞修复建议
临时性缓解措施：经安全研究人员发现，采用修改 JVM 参数、系统环境变量、修改配置, 三种缓解措施均在绕过风险，建议未修复或者已经采用了缓解措施的用户参考修复方案对漏洞进行修复。
彻底修复漏洞:
方案一、研发代码修复：升级到官方提供的 log4j-2.16.0 版本
方案二、生产环境修复：https://github.com/zhangyoufu/log4j2-without-jndi 由长亭工程师提供的删除了 JndiLookup.class 的对应版本直接替换重启即可。（如果不放心网上下载的版本，也可以自己手动解压删除：zip -q -d log4j-core-*.jar org/apache/logging/log4j/core/lookup/JndiLookup.class 删除jar包里的这个漏洞相关的class，然后重启服务即可）

v3 版本，支持 Windows/Linux/Mac 多种操作系统，i386/x86-64/arm 等多种架构，而且扫描速度大大加快。强烈推荐使用 v3 版本，使用方式：

1. 检测单个文件
sudo ./log4j2_local_scanner -root ./log4j-core-2.10.0.jar
2. 检测单个目录
sudo ./log4j2_local_scanner -root /var/www/html/
3. 检测系统所有 jar 文件
sudo ./log4j2_local_scanner -root /
4. Windows 服务器扫描 C 盘，使用系统权限执行
log4j2_local_scanner -root C:\
5. 检测并将有漏洞的文件写入日志文件
sudo ./log4j2_local_scanner -root / -output scan.log
