```shell
MySQL5.5默认的四个数据库用途
 MySQL5.5默认的四个数据库用途 

INFORMATION_SCHEMA
数据库INFORMATION_SCHEMA：提供了访问数据库元数据的方式。

　　元数据是关于数据的数据，如数据库名或表名，列的数据类型，或访问权限等。有些时候用于表述该信息的其他术语包括“数据词典”和“系统目录”。换句换说，information_schema是一个信息数据库，它保存着关于MySQL服务器所维护的所有其他数据库的信息。(如数据库名，数据库的表，表栏的数据类型与访问权 限等。) 在INFORMATION_SCHEMA中，有几张只读表。它们实际上是视图，而不是基本表。

PERFORMANCE_SCHEMA
数据库PERFORMANCE_SCHEMA：主要用于收集数据库服务器性能参数。并且库里表的存储引擎均为PERFORMANCE_SCHEMA，而用户是不能创建存储引擎为PERFORMANCE_SCHEMA的表。

参考：https://www.cnblogs.com/zhoujinyi/p/5236705.html

mysql
数据库mysql：这个是mysql的核心数据库，类似于sql server中的master表，主要负责存储数据库的用户、权限设置、关键字等mysql自己需要使用的控制和管理信息。不可以删除，如果对mysql不是很了解，也不要轻易修改这个数据库里面的表信息。

test
数据库是test：这个是安装时候创建的一个测试数据库，和它的名字一样，是一个完全的空数据库，没有任何表，可以删除。

----------------------------------------------------------------------------------------------------------------------------
1.终端登录mysql数据库，显示全部数据库（或者直接用客户端工具展示）,如下：

show databases; 

四个系统自带库为：
information_schema、mysql、performance_schema、sys
； 

5.6版本自带的库为：
information_schema、mysql、performance_schema、test
。

2.information_schema
.information_schema提供了访问数据库元数据的方式。(元数据是关于数据的数据，如数据库名或表名，列的数据类型，或访问权限等。有时用于表述该信息的其他术语包括“数据词典”和“系统目录”。)
.换句换说，information_schema是一个信息数据库，它保存着关于MySQL服务器所维护的所有其他数据库的信息。(如数据库名，数据库的表，表栏的数据类型与访问权 限等。) 在INFORMATION_SCHEMA中，有几张只读表。它们实际上是视图，而不是基本表。
.查看具体表： 

.TABLES：提供了关于数据库中的表的信息（包括视图），详细表述了某个表属于哪个schema，表类型，表引擎，创建时间等信息，show tables from schemaname的结果取之此表。
.COLUMNS：提供了表中的列信息，详细表述了某张表的所有列以及每个列的信息，show columns from schemaname.tablename的结果取之此表。
.STATISTICS：提供了关于表索引的信息，
show index from schemaname.tablename的结果取之此表。
USER_PRIVILEGES（用户权限）：给出了关于全程权限的信息，该信息源自mysql.user授权表(非标准表)。
SCHEMA_PRIVILEGES（方案权限）：给出了关于方案（数据库）权限的信息，该信息来自mysql.db授权表(非标准表)。
TABLE_PRIVILEGES（表权限）：给出了关于表权限的信息，该信息源自mysql.tables_priv授权表(非标准表)。
COLUMN_PRIVILEGES（列权限）：给出了关于列权限的信息，该信息源自mysql.columns_priv授权表(非标准表)。
CHARACTER_SETS（字符集）：提供了mysql实例可用字符集的信息，SHOW CHARACTER SET结果集取之此表。
COLLATIONS：提供了关于各字符集的对照信息。
COLLATION_CHARACTER_SET_APPLICABILITY：指明了可用于校对的字符集，这些列等效于SHOW COLLATION的前两个显示字段。
TABLE_CONSTRAINTS：描述了存在约束的表，以及表的约束类型。
KEY_COLUMN_USAGE：描述了具有约束的键列。
ROUTINES：提供了关于存储子程序（存储程序和函数）的信息，此时，ROUTINES表不包含自定义函数（UDF），名为“mysql.proc name”的列指明了对应于INFORMATION_SCHEMA.ROUTINES表的mysql.proc表列。
VIEWS：给出了关于数据库中的视图的信息，需要有show views权限，否则无法查看视图信息。
TRIGGERS：提供了关于触发程序的信息，必须有super权限才能查看该表。

3.mysql
mysql的核心数据库，类似于sql server中的master表，主要负责存储数据库的用户、权限设置、关键字等mysql自己需要使用的控制和管理信息。(常用的，在mysql.user表中修改root用户的密码)。

 4.performance_schema 

主要用于收集数据库服务器性能参数。并且库里表的存储引擎均为PERFORMANCE_SCHEMA，而用户是不能创建存储引擎为PERFORMANCE_SCHEMA的表。MySQL5.7默认是开启的。

5.sys 

Sys库所有的数据源来自：performance_schema。目标是把performance_schema的把复杂度降低，让DBA能更好的阅读这个库里的内容。让DBA更快的了解DB的运行情况。











```

