```shell
如何调整weblogic内存大小

我们经常在使用WebLoigc部署应用程序后，发现程序运行速度并不是很快，遇到这种情况我们可以尝试调整启动时分配的内存，设置方法有两种：
    一、在../domain/startWebLoigc.***文件中设置
    在startWebLogic.bat或startWebLogic.sh中找到以下内容，在其下方添加需要设置的内存

Java代码 复制代码
echo ***************************************************   
echo *  To start WebLogic Server, use a username and   *   
echo *  password assigned to an admin-level user.  For *   
echo *  server administration, use the WebLogic Server *   
echo *  console at http://[hostname]:[port]/console    *   
echo ***************************************************  
echo ***************************************************
echo *  To start WebLogic Server, use a username and   *
echo *  password assigned to an admin-level user.  For *
echo *  server administration, use the WebLogic Server *
echo *  console at http://[hostname]:[port]/console    *
echo ***************************************************
    （1）Windows环境：

Java代码 复制代码
set MEM_ARGS=-Xms512m -Xmx768m  
set MEM_ARGS=-Xms512m -Xmx768m
   （2）Linux/Unix环境：

 

Java代码 复制代码
MEM_ARGS="-Xms512m -Xmx768m"  
MEM_ARGS="-Xms512m -Xmx768m"
 

 

    二、在../weblogic81/common/bin/commEnv.***文件中设置
    在commEnv.bat或commEnv.sh找到以下内容，对其进行修改
    （1）Windows环境：

 

Java代码 复制代码
:sun   
if "%PRODUCTION_MODE%" == "true" goto sun_prod_mode   
set JAVA_VM=-client   
set MEM_ARGS=-Xms32m -Xmx200m -XX:MaxPermSize=128m   
set JAVA_OPTIONS=%JAVA_OPTIONS% -Xverify:none   
goto continue  
:sun_prod_mode   
set JAVA_VM=-server   
set MEM_ARGS=-Xms32m -Xmx200m -XX:MaxPermSize=128m   
goto continue  
:sun
if "%PRODUCTION_MODE%" == "true" goto sun_prod_mode
set JAVA_VM=-client
set MEM_ARGS=-Xms32m -Xmx200m -XX:MaxPermSize=128m
set JAVA_OPTIONS=%JAVA_OPTIONS% -Xverify:none
goto continue
:sun_prod_mode
set JAVA_VM=-server
set MEM_ARGS=-Xms32m -Xmx200m -XX:MaxPermSize=128m
goto continue
   通过修改其中的内存即可，这里选择修改的JDK为sun公司的，weblogic中自带的jrockit JDK修改可以查看:bea中内容。

    （2）Linux/Unix环境：

 

Java代码 复制代码
Sun)   
        JAVA_VM=-server   
        MEM_ARGS="-Xms32m -Xmx200m -XX:MaxPermSize=128m"  
 ;;   
和   
Sun)   
        JAVA_VM=-client   
        MEM_ARGS="-Xms32m -Xmx200m -XX:MaxPermSize=128m"  
        JAVA_OPTIONS="${JAVA_OPTIONS} -Xverify:none"  
 ;;  
Sun)
    	JAVA_VM=-server
    	MEM_ARGS="-Xms32m -Xmx200m -XX:MaxPermSize=128m"
 ;;
和
Sun)
    	JAVA_VM=-client
    	MEM_ARGS="-Xms32m -Xmx200m -XX:MaxPermSize=128m"
        JAVA_OPTIONS="${JAVA_OPTIONS} -Xverify:none"
 ;;
   通过修改其中的内存即可，这里选择修改的JDK为sun公司的，weblogic中自带的jrockit JDK修改可以查看BEA)中内容。
    第二种方法可以成功，原因在于在startWebLogic文件中有调用的内容：call "%WL_HOME%/common/bin/commEnv.cmd"。
    简单解释一下设置的两个参数：Xms为最小内存，不能超过物理内存的25%；Xmx为最大内存-Xmx 不能超过1.8G(32位的CPU)。
```

