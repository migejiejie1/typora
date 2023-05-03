snmp-agent mib-view

**命令功能**
snmp-agent mib-view命令用来创建或者更新视图的信息。

undo snmp-agent mib-view命令用来取消当前设置。

缺省情况下，视图名为ViewDefault，OID为1.3.6.1。

**命令格式**
snmp-agent mib-view { excluded | included } view-name oid-tree

undo snmp-agent mib-view view-name

**参数说明**

| 参数      | 参数说明            | 取值                                                    |
| --------- | ------------------- | ------------------------------------------------------- |
| excluded  | 表示排除该MIB子树。 | -                                                       |
| included  | 表示包括该MIB子树。 | -                                                       |
| view-name | 视图名。            | 字符串形式，不支持空格，区分大小写，长度范围是1 ～ 32。 |
| oid-tree  | MIB对象子树的OID。  | 字符串形式，长度范围是1 ～ 255。                        |



**视图**
系统视图

**缺省级别**
3：管理级

**使用指南**
snmp-agent mib-view命令用来更新或创建视图，目前该命令不仅支持以变量OID的字符串作为参数输入，同时还支持以节点名作为参数输入。

**使用实例**

创建一个视图包含MIB-II的所有对象。

[Base] snmp-agent mib-view included mib2view 1.3.6.1.2.1



https://support.huawei.com/enterprise/zh/doc/EDOC1000015867/23674a74