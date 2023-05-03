```shell
WebLogic安装有三种方式：
1、图形界面安装
最典型的就是windows上安装直接点开就是。
2、命令行安装
windows下可以选择在CMD界面下进行，Linux下的默认安装方式，需交互（输入目录，点击确认什么的）
3、静默安装
可实现全程无人值守式的安装，批量自动化部署首选方案。
本次只记录静默安装方式：
静默安装的方法在12c前和12以后是有差别的，本篇只记录11g和12c两种安装方式，其他版本不测试，不保证记录方法通用其他版本。
a、weblogic 11g的静默安装
首先获取11g的安装包，设置好java环境变量。
静默安装命令参考：
java -jar wls1036_generic.jar -mode=silent -silent_xml=/home/weblogic/silent_install.xml
静默安装最重要的是silent_install.xml文件，当然，文件名也不重要，可以随意取名，内容才重要。
silent_install.xml文件中主要就是指定weblogic安装过程中一些需要指定的东西，如安装目录等，本次测试安装内容如下：
<bea-installer>
<input-fields>
<data-valuename="BEAHOME"value="/app/weblogic"/>
<data-valuename="WLS_INSTALL_DIR"value="/app/weblogic/wlserver_10.3"/>
<data-valuename="COMPONENT_PATHS"value="WebLogic Server/Core Application Server|WebLogic Server/Administration Console|WebLogic Server/Configuration Wizard and Upgrade Framework|WebLogic Server/Web 2.0 HTTP Pub-Sub Server|WebLogic Server/WebLogic JDBC Drivers|WebLogic Server/Third Party JDBC Drivers|WebLogic Server/WebLogic Server Clients|WebLogic Server/WebLogic Web Server Plugins|WebLogic Server/UDDI and Xquery Support|WebLogic Server/Server Examples"/>
<data-valuename="INSTALL_NODE_MANAGER_SERVICE"value="yes"/>
<data-valuename="NODEMGR_PORT"value="5556"/>
<data-valuename="INSTALL_SHORTCUT_IN_ALL_USERS_FOLDER"value="yes"/>
<!--<data-value name="LOCAL_JVMS" value="C:\jrockit_160_05|D:\jdk160_05″/>-->
    </input-fields>
</bea-installer>

ps:copy注意xml格式，value内容都是一行写完的，别换行
命令执行过程中输出内容部分如下：
INFO: succeed: add template/app/weblogic/wlserver_10.3/common/templates/applications/medrec-spring.jar to domain
Jul 24,20185:44:25 AM [THREAD: runScript] com.oracle.cie.domain.script.ScriptExecutor outputINFO: validateConfig "KeyStorePasswords"
Jul 24,20185:44:25 AM [THREAD: runScript] com.oracle.cie.domain.script.ScriptExecutor outputINFO: succeed: validateConfig "KeyStorePasswords"
Jul 24,20185:44:25 AM [THREAD: runScript] com.oracle.cie.domain.script.ScriptExecutor outputINFO: update domain
Jul 24,20185:44:29 AM [THREAD: Thread-13] com.oracle.cie.domain.TemplateImporter generateINFO: Domain Extension Successful!
Jul 24,20185:44:29 AM [THREAD: runScript] com.oracle.cie.domain.script.ScriptExecutor outputINFO: close template
Jul 24,20185:44:29 AM [THREAD: runScript] com.oracle.cie.domain.script.ScriptExecutor outputINFO: succeed: close template
Jul 24,20185:44:29 AM [THREAD: runScript] com.oracle.cie.domain.script.ScriptExecutor outputINFO: read domain from "/app/weblogic/wlserver_10.3/samples/domains/medrec-spring"
Jul 24,20185:44:29 AM [THREAD: runScript] com.oracle.cie.domain.script.ScriptExecutor outputINFO: succeed: read domain from "/app/weblogic/wlserver_10.3/samples/domains/medrec-spring"
Jul 24,20185:44:29 AM [THREAD: runScript] com.oracle.cie.domain.script.ScriptExecutor outputINFO: succeed: update domain
Jul 24,20185:44:29 AM [THREAD: runScript] com.oracle.cie.domain.script.ScriptExecutor outputINFO: close template
Jul 24,20185:44:29 AM [THREAD: runScript] com.oracle.cie.domain.script.ScriptExecutor outputINFO: succeed: close template
全程无error说明安装成功，去指定安装目录检查文件是否正确即可。
b、weblogic 12c的静默安装
weblogic12c相比较11g在静默安装上有很大的变化，包括包名、jdk建议版本、静默文件、安装命令参数等
包名：fmw_12.2.1.3.0_wls.jar
静默安装所需文件：
1、responseFile:wls.rsp
2、invPtrLoc File:oralnst.loc
其中文件1记录weblogic安装目录，安装类型等，内容如下
[ENGINE]
ResponseFile Version=1.0.0.0.0
[GENERIC]
ORACLE_HOME=/app/wlsnew
INSTALL_TYPE=WebLogic Server
[响应文件中的项一定要写全，否则会报参数不足等错误]
使用上面得配置文件报错如下:
  Verifying data......
  [ERROR] Data Insufficient to start Install.
  [ERROR] Rule_ValidateOCM_Error. Aborting Install
则使用下面完整得配置文件:

[ENGINE]
#DO NOT CHANGE THIS.
Response File Version=1.0.0.0.0
[GENERIC]
#The oracle home location. This can be an existing Oracle Home or a new Oracle Home
ORACLE_HOME=/weblogic/Oracle/Middleware
#Set this variable value to the Installation Type selected. e.g. WebLogic Server, Coherence, Complete with Examples.
INSTALL_TYPE=WebLogic Server
#Provide the My Oracle Support Username. If you wish to ignore Oracle Configuration Manager configuration provide empty string for user name.
MYORACLESUPPORT_USERNAME=
#Provide the My Oracle Support Password
MYORACLESUPPORT_PASSWORD=
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
PROXY_PWD=
#Type String (URL format) Indicates the OCM Repeater URL which should be of the format [scheme[Http/Https]]://[repeater host]:[repeater port]
COLLECTOR_SUPPORTHUB_URL=



文件2记录用户的组名，产品安装清单的目录，内容如下：
inventory_loc=/app/weblogic12c/
oraInventoryinst_group=weblogic
安装命令参考
/app/mw/jdk1.8/bin/java -jar /app/dufg/fmw_12.2.1.3.0_wls.jar  -silent -responseFile /app/dufg/wls.rsp -invPtrLoc /app/dufg/oralnst.loc
安装开始前会对系统做一些检查，如cpu，内存是否满足等，这里还有一项专门对swap分区大小进行检查，swap配额必须在500M以上，否则即使内存再大都会直接退出安装！任性！
大致的检查和报错输出如下：
启动程序日志文件为/tmp/OraInstall2018-07-25_04-45-20PM/launcher2018-07-25_04-45-20PM.log。
正在提取安装程序....... 完成
检查 CPU 速度是否大于 300 MHz。   实际为 2600.000 MHz    通过
检查交换空间: 必须大于 512 MB。   实际为 0 MB    未通过 <<<<
检查此平台是否需要 64 位 JVM。   实际为64    通过 (不需要 64 位)
检查临时空间: 必须大于 300 MB。   实际为 19100 MB    通过

某些系统先决条件检查失败。必须在满足这些要求后才能继续。
日志位于此处:/tmp/OraInstall2018-07-25_04-45-20PM/launcher2018-07-25_04-45-20PM.log。
安装正常完成日志如下：
启动程序日志文件为/tmp/OraInstall2018-07-25_04-59-54PM/launcher2018-07-25_04-59-54PM.log。
正在提取安装程序....... 完成
检查 CPU 速度是否大于 300MHz。   实际为 2600.000MHz    通过
检查交换空间: 必须大于 512MB。   实际为 1023MB    通过
检查此平台是否需要 64 位 JVM。   实际为64    通过 (不需要 64 位)
检查临时空间: 必须大于 300MB。   实际为 17260MB    
通过准备从/tmp/OraInstall2018-07-25_04-59-54PM启动 OracleUniversalInstaller
日志:/tmp/OraInstall2018-07-25_04-59-54PM/install2018-07-25_04-59-54PM.log
版权所有 (c)1996,2017,Oracle 和/或其附属公司。保留所有权利。
正在读取响应文件...
跳过软件更新
开始检查:CertifiedVersions
预期的结果: oracle-6, oracle-7, redhat-7, redhat-6,SuSE-11,SuSE-12之一
实际结果: redhat-null
检查完成。此次检查的总体结果为: 通过
CertifiedVersions 检查: 成功。

开始检查:CheckJDKVersion
预期的结果:1.8.0_131
实际结果:1.8.0_131
检查完成。此次检查的总体结果为: 通过
CheckJDKVersion 检查: 成功。

已启用此会话的验证。
正在验证数据
复制文件
完成百分比:10
完成百分比:20
完成百分比:30
完成百分比:40
完成百分比:50
完成百分比:60
完成百分比:70
完成百分比:80
完成百分比:90
完成百分比:100
或
[-----------20%----------40%----------60%----------80%--------100%]
OracleFusionMiddleware12c WebLogicServer 和 Coherence12.2.1.3.0 的 安装 已成功完成。
日志已成功复制到/app/dufg/wls12log/logs。
安装过程很快，2分钟足够！
安装完之后安装目录下文件如下：
cfgtoollogs/  inventory/  oracle_common/  oui/      wlserver/coherence/    OPatch/     oraInst.loc     root.sh*
```

