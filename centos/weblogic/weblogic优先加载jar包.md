```shell
weblogic中优先加载应用中的jar包 prefer-application-packages

其他帮助连接：http://shuwen.iteye.com/blog/1124220

仅针对10.3及以上版本。 

在WEB-INF下面添加weblogic.xml文件。

<?xml version="1.0" encoding="UTF-8"?>
<weblogic-web-app
        xmlns="http://xmlns.oracle.com/weblogic/weblogic-web-app"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
http://java.sun.com/xml/ns/javaee/ejb-jar_3_0.xsd
http://xmlns.oracle.com/weblogic/weblogic-web-app
http://xmlns.oracle.com/weblogic/weblogic-web-app/1.2/weblogic-web-app.xsd">
    <jsp-descriptor>
        <working-dir>app_workingDir</working-dir>
    </jsp-descriptor>
    <container-descriptor>

        <!--<prefer-web-inf-classes>true</prefer-web-inf-classes>-->

        <prefer-application-packages>
            <package-name>org.apache.commons.lang.*</package-name>
            <package-name>antlr.*</package-name>
            <package-name>org.hibernate.*</package-name>
            <package-name>javax.persistence.*</package-name>
        </prefer-application-packages>

    </container-descriptor>
    <context-root>/app</context-root>
</weblogic-web-app>

其中prefer-web-inf-classes和prefer-application-packages只能二选一。 
使用此方法对hibernate jpa2.0加载时可不用修改weblogic启动脚本的CLASSPATH。 
注意xml文件的xsd文件声明必须正确。 

我用此方法解决了在weblogic10.3.6和hibernate3.6.10的jpa jar包冲突。 
Invocation of init method failed; nested exception is java.lang.ArrayStoreException: sun.reflect.annotation.EnumConstantNotPresentExceptionProxy 
如只设定prefer-web-inf-classes为true 也不能解决以上问题。 

============================================================

<?xml version="1.0" encoding="UTF-8"?>
<weblogic-web-app>
 <container-descriptor>
  <prefer-web-inf-classes>true</prefer-web-inf-classes>
 </container-descriptor>
</weblogic-web-app>
============================================================


<?xml version = '1.0' encoding = 'UTF-8'?>
<weblogic-web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.bea.com/ns/weblogic/weblogic-web-app http://www.bea.com/ns/weblogic/weblogic-web-app/1.0/weblogic-web-app.xsd"
	xmlns="http://www.bea.com/ns/weblogic/weblogic-web-app">
	<context-root>test</context-root>
	<container-descriptor>
		<!-- 优先加载web工程中的包 -->
		<prefer-web-inf-classes>true</prefer-web-inf-classes>
	</container-descriptor>
</weblogic-web-app>


```

