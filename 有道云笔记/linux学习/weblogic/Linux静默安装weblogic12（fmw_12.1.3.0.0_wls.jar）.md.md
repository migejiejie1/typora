```shell
Linux静默安装weblogic12（fmw_12.1.3.0.0_wls.jar）
lzc_jack 2016-05-13 原文
1、安装JDK环境
#tar zxvf jdk-7u80-linux-x64.gz
#.0_80 /usr/local/jdk1..0_80/
2、创建安装用户
#useradd weblogic
#su - weblogic
3、配置JAVA环境变量
$vi .bash_profile
export JAVA_HOME=/usr/local/jdk1..0_80
export JRE_HOME=/usr/local/jdk1..0_80/jre
export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH
export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH
export ORACLE_HOME=/bea
4、创建响应文件wls.rsp
响应文件中的项一定要写全，否则会报奇怪的错误。
[ENGINE]
#DO NOT CHANGE THIS.
Response File Version=
[GENERIC]]
#The oracle home location. This can be an existing Oracle Home or a new Oracle Home
ORACLE_HOME=/bea
#Set this variable value to the Installation Type selected. e.g. WebLogic Server, Coherence, Complete with Examples.
INSTALL_TYPE=WebLogic Server
#Provide the My Oracle Support Username. If you wish to ignore Oracle Configuration Manager configuration provide empty string for user name.
MYORACLESUPPORT_USERNAME=
#Provide the My Oracle Support Password
MYORACLESUPPORT_PASSWORD=<SECURE VALUE>
#Set this to true if you wish to decline the security updates. Setting this to true and providing empty string for My Oracle Support username will ignore the Oracle Configuration Manager configuration
DECLINE_SECURITY_UPDATES=true
#Set this to true if My Oracle Support Password is specified
SECURITY_UPDATES_VIA_MYORACLESUPPORT=false
#Provide the Proxy Host
PROXY_HOST=
#Provide the Proxy Port
PROXY_PORT=
#Provide the Proxy Username
PROXY_USER=
#Provide the Proxy Password
PROXY_PWD=<SECURE VALUE>
#Type String (URL format) Indicates the OCM Repeater URL which should be of the format [scheme[Http/Https]]://[repeater host]:[repeater port]
COLLECTOR_SUPPORTHUB_URL=
5、创建Loc文件oraInst.loc
inventory_loc=/home/weblogic/oraInventory
#用户的组名称，根据实际的修改
inst_group=weblogic
6、执行安装
java -jar fmw_12..0_wls.jar -silent -responseFile /home/weblogic/wls.rsp -invPtrLoc /home/weblogic/oraInst.loc
启动程序日志文件为/tmp/OraInstall2016--13_01--56PM/launcher2016--13_01--56PM.log。
正在提取文件.........
启动 Oracle Universal Installer
检查 CPU 速度是否大于  MHz。   实际为 2400.217 MHz    通过
检查交换空间: 必须大于  MB。   实际为  MB    通过
检查此平台是否需要  位 JVM。   实际为64    通过 (不需要  位)
检查临时空间: 必须大于  MB。   实际为  MB    通过
准备从/tmp/OraInstall2016--13_01--56PM启动 Oracle Universal Installer
日志:/tmp/OraInstall2016--13_01--56PM/install2016--13_01--56PM.log
版权所有 (c) , , Oracle 和/或其附属公司。保留所有权利。
正在读取响应文件...
开始检查: CertifiedVersions
预期的结果: enterprise-,enterprise-,enterprise-,redhat-,redhat-,redhat-,SuSE-11之一
实际结果: enterprise-
检查完成。此次检查的总体结果为: 通过
CertifiedVersions 检查: 成功。
开始检查: CheckJDKVersion
预期的结果: 1.7.0_15
实际结果: 1.7.0_80
检查完成。此次检查的总体结果为: 通过
CheckJDKVersion 检查: 成功。
已启用此会话的验证。
正在验证数据...
正在复制文件...
可以在以下位置找到本次安装会话的日志:
 /tmp/OraInstall2016--13_01--56PM/install2016--13_01--56PM.log
-----------%----------%----------%----------%--------%
Oracle Fusion Middleware 12c WebLogic Server 和 Coherence  的 安装 已成功完成。
日志已成功复制到/home/weblogic/oraInventory/logs。
7、创建域
export MW_HOME="/bea"
export WL_HOME="/bea/oracle_common"export CONFIG_JVM_ARGS='-Djava.security.egd=file:/dev/urandom'  --执行该命令，避免创建域过慢bash$ pwd
/bea/wlserver/common/bin
bash$ ./commEnv.sh
bash$ ./wlst.sh
Java HotSpot(TM) -Bit Server VM warning: ignoring option MaxPermSize=256m; support was removed in 8.0
Initializing WebLogic Scripting Tool (WLST) ...
Welcome to WebLogic Server Administration Scripting Shell
Type help() for help on available commands
wls:/offline>readTemplate('/bea/wlserver/common/templates/wls/wls.jar')
wls:/offline/base_domain>cd('Servers/AdminServer')
wls:/offline/base_domain/Server/AdminServer>set('ListenAddress','')
wls:/offline/base_domain/Server/AdminServer>set()
wls:/offline/base_domain/Server/AdminServer>cd('../..')
wls:/offline/base_domain>cd('Security/base_domain/User/weblogic')
wls:/offline/base_domain/Security/base_domain/User/weblogic>cmo.setPassword('weblogic12')
wls:/offline/base_domain/Security/base_domain/User/weblogic>setOption('OverwriteDomain', 'true')
wls:/offline/base_domain/Security/base_domain/User/weblogic>writeDomain('/bea/user_projects/domains/hbintf_domain')
closeTemplate()
exit()
8、启动
$cd /bea/user_projects/domains/hbintf_domain
$./startWeblogic.sh
参考资料：
https://docs.oracle.com/middleware/1213/core/OUIRF/silent.htm#OUIRF323
```

