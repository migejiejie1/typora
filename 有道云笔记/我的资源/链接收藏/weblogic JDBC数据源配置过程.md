数据源配置过程:可以选择“一般数据源”或“多数据源”

 

JDBC 数据源是指与 JNDI 树 (通过一组 JDBC 连接提供数据库连接) 绑定的对象。应用程序可在 JNDI 树上查找数据源, 然后借用一个与数据源的数据库连接。

此页概括了已在该域中创建的 JDBC 数据源对象。

一、选择“数据源”——“锁定并编辑”——“新建”，这里可以选择“一般数据源”或“多数据源”

![image-20221017202042626](../../../Image/image-20221017202042626.png)

二、定义数据源名称、JNDI名称、和数据库类型

![image-20221017202054587](../../../Image/image-20221017202054587.png)

三、数据库驱动程序  选择“Oracle's Driver(Thin) for Instance connections;Versions:Any”

![image-20221017202105844](../../../Image/image-20221017202105844.png)

四、保持默认选项“支持全局事物处理”

![image-20221017202119394](../../../Image/image-20221017202119394.png)

五、填写数据库信息

![image-20221017202131916](../../../Image/image-20221017202131916.png)

六、测试数据库配置是否可用，测试连接

![image-20221017202143267](../../../Image/image-20221017202143267.png)

七、选择部署目标或集群

![image-20221017202153970](../../../Image/image-20221017202153970.png)

八、完成并激活更改，即可。至此，单数据源配置完毕

![image-20221017202205144](../../../Image/image-20221017202205144.png)

九、多数据源配置：建立多数据源，定义数据源名称、JDNI名称、算法类型

![image-20221017202216454](../../../Image/image-20221017202216454.png)

十、选择目标或集群

![image-20221017202227929](../../../Image/image-20221017202227929.png)

十一、选择数据源类型，保持默认“非XA驱动程序”

![image-20221017202239746](../../../Image/image-20221017202239746.png)

十二、选择对应的数据源

![image-20221017202250698](../../../Image/image-20221017202250698.png)

十二、完成并激活更改，即可。

![image-20221017202302584](../../../Image/image-20221017202302584.png)

