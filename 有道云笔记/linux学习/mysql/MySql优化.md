SQL优化

\--

通过show status命令了解各种sql的执行效率

查看本session的sql执行效率 

show status like 'Com_%'; _

_查看全局的统计结果 _

_SHOW GLOBAL STATUS LIKE  'Com_%' 

查看服务器的状态 

show global status;     

123456



结果

- Com_select:执行select操作的次数，依次查询之累加1
- Com_insert:执行insert操作的次数，对于批量插入的insert操作，只累加依次
- Com_update:执行update操作的此时
- Com_delete:执行delete的次数

上面的参数是对所有存储引擎的表进行累计，下面参数是针对InnoDB存储引擎的，累加的算法也是略有不同的

- Innodb_rows_read:SELECT查询返回的行数
- Innodb_rows_insered:执行inser操作插入的行数
- Innodb_rows_updated:执行UPDATE操作更新的行数
- Innodb_rows_deleted执行DELETE操作删除的行数

通过上述的参数可以了解当前数据库的应用是插入更新为主还是查询操作为主，以及各类的SQL的执行比例是多少。对于更新操作的计算，是对执行次数的计数，无论提交还是回滚都会进行累加对于事务形的应用，通过Com_commit和Com_rollback可以了解事务提交和回滚的情况，对于回滚操作非常频繁的数据库，可能意味着应用编写存在的问题

- Connections:试图连接MySql服务器的次数
- Uptime：服务器工作时间
- Slow_queries:慢查询的次数

定位执行效率低的SQL语句

1. 通过慢查询日志定位那些执行效率较低的sql语句，用--log-show-queries[=file_name]选项去启动，mysqlId写一个包含所有执行时间超过long_querty_time秒的sql语句的日志文件
2. 慢查询日志在查询结束后才记录，所以在应用反应执行效率出现问题的时候查询慢查询日志并不能定位问题，可以使用show processlist命令查看当前Mysql在进行的线程，包括线程的状态，是否锁表等，可以实时查看sql的执行情况，同时对一些锁表进行优化

通过explain分析执行sql的执行计划

explain或者desc获取mysql如何执行select语句的信息

explain select * from user; 结果 包含，id，select_type,table.type.... 123

每个列说明

- select_type：表示SELECT的类型，常见的取值有simple（简单表，即不用表连接或者子查询），primary（主查询，即外部查询），union（union中的第二个或者后面的查询语句），subquery（子查询中的第一个select）等
- table:输出结果集
- type：表示Mysql在表中找到所需行的方式，或者叫访问类型，常见类型如：all,index,range,ref,eq_ref,const,system,null,从做到右，性能由差到好

type=all，全表扫描，mysql遍历全表来找到匹配的行 explain select * from film where rating>9 type=index,索引全扫描，MySQL遍历整个索引来查询匹配的行 explain select title from film type=range,索引范围扫描，常见<,<=,>,>=,between explain select * from payment where customer_id>=300 and customer_id<=350 type=ref，使用费索引扫描或唯一索引的前缀扫描 explain select * from payment where customer_id=350 type=eq_ref,类似ref，区别在于使用的索引是唯一索引，对于每个索引键值，表中有一条记录匹配；简单来说就是多表连接使用primary key或者unique index作为关联条件 explain select * from film a,film_text b where a.film_id=b.film_id type=const/system,单表中最多有一个匹配行，查询起来非常迅速，索引这个匹配行中的其他列的值可以被优化器在当前查询中当做常量来处理,例如根据主键primary key或者唯一一个索引来查询 type null,mysql不用访问数据库直接得到结果 explain select 1 from dual where 1 1234567891011121314

mysql 4.1引入了explain extended命令，通过explain extended 加上show warnings可以查看mysql 真正被执行之前优化器所做的操作

explain select * from users; show warnings; 可以从warning的字段中能够看到，会去除一些恒成立的条件，可以利用explain extended的结果来迅速的获取一个更清晰易读的sql语句 123

通过show profile 分析sql

查看mysql是否支持profile select @@have_profiling; 默认profiling是关闭的，可以通过set语句在session级别启动profiling; select @@profiling; set profiling=1; 1234567

通过profile，我们能够更清楚的了解sql执行的过程。例如我们知道，MyISAM表有表元数据的缓存（例如行，即COUNT(*)值),对于MyISAM表的COUNT(*)是不需要消耗太多资源，而对于Innodb来说，就没有这种元数据，CONUT（*）执行的比较慢

select count(*) from users; 执行完毕查看  show profiles 可以查看之前的queryid show profile for query 2； 可以查看执行过程中线程的每个状态和消耗时间 其中 sendingdata 状态表示mysql线程开始访问数据行并把结果返回给客户端，而不仅仅是返回给客户端，由于在sending data状态下，mysql线程往往需要做大量的磁盘读取操作；所以经常是整个查询中最耗时的状态 12345678

mysql 支持进一步选择all,cpu,block io,context,switch,page faults等明细来查看mysql在使用什么资源上耗费了过高的时间，例如，选择查看cpu的耗费时间

show profile cpu for query 6; 1

对比MyISAM的操作，同样执行count(*)操作，检查profile，Innodb表经历了Sending data状态，而MyISAM的表完全不需要访问数据

如果对Mysql 源码感兴趣，可以通过show profile source for query查看sql解析执行过程的每个步骤对应的源码文件

show profile source for query 6 1

通过trace分析优化器如何

MySql 5.6提供对sql的跟踪trace，通过trace文件能够进一步了解为什么优化器选择A执行计划而不选择B执行计划，帮助我们更好地了解优化器的行为

使用方式 首先打开trace，设置格式为json，设置trace最大能够使用的内存，避免解析过程中因为默认内存小而不能完整显示 set optimizer_trace="enabled=on",end_markers_in_json=on; set optimizer_trace_max_mem_size=1000000; 接下来执行trace的sql语句， select * from ....where.... 最后检查information_schema.optimizer_trace就可以知道Mysql如何执行sql select * from information_schema.optimizer_trace 123456789

索引问题

mysql提供四种索引

- B-Tree索引:最常见的的索引，大部分引擎支持B树索引
- HASH索引:只有Memory引擎支持，使用场景简单
- R-Tree索引:空间索引是MyISAM的一个特殊索引类型，主要用于地理空间数据类型，通常使用较少
- Full-text：全文索引也是MyISAM的一个特殊索引，主要用于全文索引，InnoDb从MySql5.6开始提供支持全文索引

MySql目前不支持函数索引，但是能对列的前面某一部分进行索引，例如标题title字段，可以只取title的前10个字符索引，这样的特性大大缩小了索引文件的大小，但前缀索引也有缺点，在排序order by和分组group by操作的时候无法使用

create index idx_title on film(title(10)); 1

| 索引          | MyISAM引擎 | InnoDB引擎 | Memory引擎 |
| ------------- | ---------- | ---------- | ---------- |
| B-Tree索引    | 支持       | 支持       | 支持       |
| HASH索引      | 不支持     | 不支持     | 支持       |
| R-Tree索引    | 支持       | 不支持     | 不支持     |
| Full-text索引 | 支持       | 暂不支持   | 不支持     |

常用的索引就是B-tree索引和hash索引，资只有memory引擎支持HASH索引，hash索引适用于key-value查询，通过hash索引比B-tree索引查询更加迅速，但是hash索引不支持范围查找例如<><==,>==等操作，如果使用memory引擎并且where不使用=进行 索引列，就不会用的索引。Memory只有在"="的条件下才会使用索引

简单的优化方法

本语句可以用于分析和存储表的关键字分布，分析的结果可以使得系统得到准确的统计信息使得sql，能够生成正确的执行计划。如果用户感觉实际执行计划并不预期的执行计划，执行一次分析表可能会解决问题

analyze table payments; 1

检查表：检查表：检查表的作用是检查一个表或多个表是否有错误,也可以检查视图是否错误

check table payment; 1

优化表:如果删除了表的一大部分，或者如果已经对可变长度的行表（含varchar、blob、text列）的表进行改动，则使用optimize 进行表优化，这个命令可以使表中的空间碎片进行合并、并且可以消除由于删除或者更新造成的空间浪费

optimize table payment; 1

对于innodb引擎的表，可以通过设置innodb_file_per_taable参数，设置InnoDb为独立表空间模式，这样每个数据库的每个表都会生成一个独立的idb文件，用于存储表的数据和索引，可以一定程度减少Innodb表的空间回收问题,另外，在删除大量数据后，Innodb表可以通过alter table但是不锈钢引擎方式来回收不用的空间

alter table payment enigine=innodb; 1

ANALYZE,CHECK,OPTIMIZE,ALTER TABLE执行期间都是对表进行锁定，因此要在数据库不频繁的时候执行相关的操作

常用SQL的优化

大批量的插入数据

当用load导入数据，适当的设置可以提供导入的速度

对于MyISAM存储引擎的表，可以通过以下方式快速导入大量的数据

alter table tab_name disable keys; loading the data alter table tab_name disable keys; 123

disable keys和enable keys 用来打开或者关闭MyISAM表非索引的更新。在导入大量的数据到一个非空的MyISAM表，通过设置这两个命令，可以提高导入的效率

对于Innodb类型的表不能使用上面的方式提高导入效率

因为Innodb类型的表是按照主键的顺序保存，所有将导入的数据按照主键的顺序排序，可以有效地提高导入数据的效率

在导入数据强执行SET UNIQUE_CHECKS=0，关闭唯一性校验，在导入结束后执行SET UNIQUE_CHECKS=1.恢复唯一性校验，可以提高导入的效率，如果应用使用自动提交的方式，建议在导入前执行SET AUTOCOMMIT=0时，关闭自动提交，导入结束后再执行SET AUTOCOMMIT=1，打开自动提交，也可以提高导入的效率

优化insert语句

- 如果同时从一个客户端插入很多行，应尽量使用多个值表的insert语句，这种方式将大大缩减客户端与数据库之间的连接、关闭等消耗，使得效率比分开执行的单个insert语句快（大部分情况下，使用多个值表的insert语句那比单个insert语句快上好几倍）。

insert into test values(1,2),(1,3)... 1

- 如果从不同客户插入很多行，可以通过使用insert delayed语句提高更高的速度，delayed的含义是让insert语句马上执行，其实数据都被放到内存的队列中，并没有真正写入磁盘，这比每条语句分别插入要快的多；LOW_PRIORITY刚好相反，在所有其他用户对表的读写完成后才可以进行
- 将索引文件和数据文件分在不同的磁盘上存放（利用建表中的选项）
- 如果进行批量插入，可以通过增加bulk_insert_buffer_size变量值的方法来通过速度，但是，这只能对MyISAM表使用。
- 当从一个文本文件装载一个表时，使用LOAD DATA INFILE。这通常比使用很多INSERT语句块快20倍

优化ORDER BY语句

MySQL有两种排序方式

- 第一种通过有序排序索引顺序扫描，这种方式在使用explain分析查询的时候显示为Using Index，不需要额外的排序，操作效率较高

explain select customer_id from customer order by store_id; 1

- 第二张通过返回数据进行排序，也就是通常说的Filesort排序，所有不是通过索引直接返回排序结果的排序豆角Filesort排序。Filesort并不代表通过磁盘文件进行排序，而只是说明进行了一个排序操作，至于排序操作是否进行了磁盘文件或临时表等，则取决于MySql服务器对排序参数的设置和需要排序数据的大小

explain select * from customer order by store_id; 1

Filesort是通过相应的排序算法，将取得的数据在sort_buffer_size系统变量设置的内存排序区进行排序，如果内存装载不下，它会将磁盘上的数据进行分块，再对各个数据块进行排序，然后将各个块合并成有序的结果集。sort_buffer_size设置的排序区是每个线程独占的，所有同一个时刻，MySql存在多个sort buffer排序区

优化目标:尽量减少额外的排序，通过索引直接返回有序数据.where和ordery by 使用相同的索引，并且order by的顺序和索引顺序相同，并且order by的字段都是升序或者都是降序。否则肯定需要额外的排序操作，这样就会出现filesort

优化group by 语句

如果查询包括group by 但用户想要避免排序结果的消耗，可以指定group by null

优化嵌套查询

子查询可以被更有效率的连接替代

explain select * from customer where customer_id not in(select customer_id from payment) explain select * from customer a left join payment b on a.customer_id=b.customer_id where b.customer id is null 123

连接之所用更有效率是因为mysql不需要在内存中创建临时表来完成这个逻辑上需要两个步骤的查询工作

优化分页查询

一般分页查询，通过创建覆盖索引能够比较好地提高性能。一个场景是"limit 1000,20",此时Mysql排序出前1020条数据后仅仅需要返回第1001到1020条记录，前1000条数据都被抛弃，查询和排序代价非常高

优化方式：可以增加一个字段last_page_record.记录上一页和最后一页的编号，通过

explain select ...where last_page_record<... desc limt .. 1

如果排序字段出现大量重复字段，不适用这种方式进行优化

MySql常用技巧

正则表达式的使用

| 序列    | 序列说明                       |
| ------- | ------------------------------ |
| ^       | 字符串的开始处进行排序         |
| $       | 在字符串的末尾处进行匹配       |
| .       | 匹配任意单个字符串，包括换行服 |
| [...]   | 匹配括号内的任意字符           |
| [^....] | 匹配不出括号内任意字符         |
| a*      | 匹配零个或多个a(包括空串)      |
| a+      | 匹配一个或多个a（不包括字符串) |
| a?      | 匹配一个或零个a                |
| a1\|a2  | 匹配a1或a2                     |
| a(m)    | 匹配m个a                       |
| a(m,)   | 匹配m个或更多a                 |
| a(m,n)  | 匹配m到n个a                    |
| a(,n)   | 匹配0到n个a                    |
| (...)   | 将模式元素组成单一元素         |

使用

select 'abcdefg' regexp '^a'; ..... 12

如果range()提取随机行

随机抽取某些行

select * from categrory order by rand() limit 5; 1

利用group by的with rollup 子句

使用Group By的with rollup可以检索更多分组聚合信息

select date_from(payment_date,'%Y-%M'),staff_id,sum(amount) from payment group by date_formate(payment_date,'%Y-%M'),staff_id; 1

用BIT GROUP FUNCTIONS做统计

使用GROUP BY语句和BIT_AND、BIT_OR函数完成统计工作，这两个函数的一般用途就是做数值之间的逻辑

------

优化数据库对象

优化表类型

表需要使用何种数据类型工具应用来判断，虽然考虑字段的长度会有一定的冗余，但是不推荐让很多字段都留有大量的冗余，这样既浪费磁盘的存储空间，同时在应用操作时也浪费物理内存mysql，可以使用函数procedure analyse对当前的表进行分析

//输出的每一类信息都对数据表中的列的数据类型提出优化建议。第二语句高数procedure anaylse不要为那些包含的值多余16个或者256个字节的enum类型提出建议，如果没有这个限制，输出的信息可能很长；ENUM定义通常很难阅读，通过输出信息，可以将表中的部分字段修改为效率更高的字段 select * from tb1_name procedure analyse(); select * from tb2_name procedure analyse(16,256); 123

通过拆分提高表的访问效率

- 重置拆分，把主码和一些列放到一个表，然后把住码和另外的列放到另一个表， 好处可以将常用的列放在一起，不常用的列放在一起，使得数据行变少，一个数据页可以存放更多的数据，在查询时会减少I/O次数，缺点：管理冗余，查询所有数据需要用join操作
- 水平拆分。根据一列或多列数据把数据行放到两个独立的表中：水平拆分会给应用增加复杂度，它通常在查询时需要多个表名，查询所有数据需要UNION操作，缺点：只要索引关键字不大，则在索引查询时，表中增加了2-3倍的数据量，查询时也增加了读一个索引的磁盘次数，所有说拆分要考虑数据量的增长速度。常用场景

- - 表很大，分割后可以降低在查询时需要读的数据和索引的页数，同时也降低了索引的层数，提高查询速度
  - 表中的数据本来就有独立性，例如表中分别记录各个地区的数据或者不同时期的数据，特别是有些数据常用，而有些数据不常用
  - 需要把数据存放在多个介质上：如账单：最近三个月数据存在一个表中，3个月之前的数据存放在另一个表，成功一年的可以存储在单独的存储介质中。

逆规范化

数据库设计时需要瞒住规范化，但是规范化程度越高，产生的关系就越多，关系越多直接结果就是表直接的连接操作越频繁，而表连接的操作是性能较低的操作，直接影响到查询的数据。

反规范化的好处在于降低连接操作的需求，降低外码和索引的数目，还可以减少表的树木，相应带来的问题可能出现数据的完整性问题。加快查询速度，但是降低修改速度。好的索引和其他方法经常能够解决性能问题，而不必采用反规范这种方法

采用的反规范化技术

- 增加冗余列：指在多个表中具有相同的列，它常用来在查询时避免连接操作
- 增加派生列：指增加的列来自其他表中的数据，由其他表中的数据经过计算生成。增加的派生列其他作业是在查询时减少连接操作，避免使用集函数
- 重新组表：指如果许多用户需要查看两个表连接出来的结果数据，则把这两个表查询组成一个表来减少连接而提高性能
- 分割表

维护数据的完整性

- 批处理维护是指对复制列或派生列的修改积累一定的时间后，运行一批处理作业或修改存储过程对复制或派生列进行修改，这只能对实时性要求不高的情况下使用
- 数据的完整性也可由应用逻辑来实现，这就要求必须在同一事务中对所有涉及的表进行增、删、改操作。用应用逻辑来实现数据的完整性风险较大，因为同一逻辑必须在所有的应用中使用和维护，容易遗漏。特别是在需求变化时，不易于维护
- 使用触发器，对数据的任何修改立即触发对复制列或者派生列的相应修改，触发器是实时的，而且相应的处理逻辑只在一个地方出现，易于维护，一般来说，是解决这类问题比较好的方法

使用中间表提高统计查询速度

对于数据量较大的表，在其上进行统计查询通常会效率很低，并且还要考虑统计查询是

否会对在线的应用产生负面影响。通常在这种情况下，使用中间表可以提高统计查询的效率

session 表记录了客户每天的消费记录，表结构如下：

CREATE TABLE session ( cust_id varchar(10) , --客户编号 cust_amount DECIMAL(16,2), --客户消费金额 cust_date DATE, --客户消费时间 cust_ip varchar(20) –客户IP 地址 ) 123456

由于每天都会产生大量的客户消费记录,所以session 表的数据量很大,现在业务部门有

一具体的需求：希望了解最近一周客户的消费总金额和近一周每天不同时段用户的消费总金

额。针对这一需求我们通过2 种方法来得出业务部门想要的结果。

方法1：在session 表上直接进行统计,得出想要的结果。

select sum(cust_amount) from session where cust_date>adddate(now(),-7); 1

方法2：创建中间表tmp_session，表结构和源表结构完全相同。

CREATE TABLE tmp_session ( cust_id varchar(10) , --客户编号 cust_amount DECIMAL(16,2), --客户消费金额 cust_date DATE, --客户消费时间 cust_ip varchar(20) –客户IP 地址 ) ; 1234567

insert into tmp_session select * from session where cust_date>adddate(now(),-7); 1

转移要统计的数据到中间表,然后在中间表上进行统计，得出想要的结果。

在中间表上给出统计结果更为合适,原因是源数据表(session 表)

cust_date 字段没有索引并且源表的数据量较大，所以在按时间进行分时段统计时效率

很低，这时可以在中间表上对cust_date 字段创建单独的索引来提高统计查询的速度。

中间表在统计查询中经常会用到，其优点如下:

1. 中间表复制源表部分数据，并且与源表相“隔离”，在中间表上做统计查询不

会对在线应用产生负面影响.

1. 中间表上可以灵活的添加索引或增加临时用的新字段,从而达到提高统计查询

效率和辅助统计查询作用。

------

锁问题

MySql三种锁的特性

- 表级锁：开销小，加锁快;不会出现死锁；锁定粒度大，发生锁冲突的概率最高，并发度最低
- 行级锁：开销大，加锁慢;会出现死锁;锁定粒度小，发生锁冲突的概率最低，并发度最高
- 页面锁：开销和加锁时间介于表锁和行锁之间；会出现死锁；锁定粒度介于表锁和行锁之间，并发度一般

不同的数据引擎支持不同的锁机制：MyISAM和MEMORY存储引擎采用的表级锁;BDB存储引擎采用的是页面锁。但也支持表级锁；Innodb存储引擎既支持行级锁，也支持表级锁，默认采用行级锁

//查询表级锁争用情况，检查table_locks_waited和table_locks_immediate状态变量来分析 show status like 'table%' //table_locks_waited 的值越高，则说明存在严重的表级锁的争用情况 123

表级锁的锁模式

|是否兼容|请求none|请求读锁|请求写锁|

| ------------- |:-------------:|

|当前处于读锁| 是|是|否|

|当前处于写锁|是|否|否|

对MyISAM表的读操作，不会阻塞其他用户对同一张表的读请求，但会阻塞对同一张表的写请求

| session_1                                           | session_2              |
| --------------------------------------------------- | ---------------------- |
| 锁定film_text的Write锁定 lock table fime_text write |                        |
| 对当前seesion做 select,insert,update...             | 对其进行查询操作select |
| 释放锁 unlock tables                                | 等待                   |
|                                                     | 获得锁，查询返回       |

MyISAM在执行查询语句前，会自动给涉及的所有表进行表加读锁，在执行更新（update,delete,insert)会自动给涉及到的表加写锁，这个过程不需要用户干预，因此不需要用户直接用lock table命令

对于给MyISAM显示加锁，一般是为了在一定程度上模拟事务操作，实现对某一个时间点多个表一致性读取

例子

一个订单表为orders，其中记录各个订单的总金额total，同时还有一个订单明显表为order_detail,其中记录有各订单每一产品的金额小计subtotal，假设我们需要检查这两个表的金额合计是否相符

select sum(total) from orders; select sum(subtotal) from order_tail; //如果不给表加锁，可能出现错误，在第一条执行的过程，第二张表发生了该表，正确的方法 lock tables orders read local,order_detail read local; select sum(total) from orders; select sum(subtotal) from order_tail; unlock  tables 1234567

注意点

在用LOCKTABLES给表显式加表锁是时，必须同时取得所有涉及表的锁，并且MySQL支持锁升级。也就是说，在执行LOCK TABLES后，只能访问显式加锁的这些表，不能访问未加锁的表；同时，如果加的是读锁，那么只能执行查询操作，而不能执行更新操作。其实，在自动加锁的情况下也基本如此，MySQL问题一次获得SQL语句所需要的全部锁。这也正是MyISAM表不会出现死锁（Deadlock Free）的原因

| session_1                                          | session_2                                   |
| -------------------------------------------------- | ------------------------------------------- |
| 获得表film_textd READ锁 lock table film_text read; |                                             |
| 可以查询select * from film_text                    | 可以查询可以查询select * from film_text     |
| 不能查询没有锁定的表 select * from film            | 可以查询或更新未锁定的表 select * from film |
| 插入或更新锁定表会提示错误 update...from film_text | 更新锁定表会等待 update...from film_text    |
| 释放锁 unlock tables                               | 等待                                        |
|                                                    | 获得所，更新成功                            |

tips

当使用LOCK TABLE时，不仅需要一次锁定用到的所有表，而且，同一个表在SQL语句中出现多少次，就要通过与SQL语句中相同的别名锁多少次，否则也会出错！

lock table actor read //会提示错误 select a.first_name..... //需要对别名分别锁定 lock table actor as read,actor as b read; 12345

并发锁

在一定条件下，MyISAM也支持查询和操作的并发进行。

MyISAM存储引擎有一个系统变量concurrent_insert，专门用以控制其并发插入的行为，其值分别可以为0、1或2。

- 当concurrent_insert设置为0时，不允许并发插入。
- 当concurrent_insert设置为1时，如果MyISAM允许在一个读表的同时，另一个进程从表尾插入记录。这也是MySQL的默认设置。
- 当concurrent_insert设置为2时，无论MyISAM表中有没有空洞，都允许在表尾插入记录，都允许在表尾并发插入记录。

可以利用MyISAM存储引擎的并发插入特性，来解决应用中对同一表查询和插入锁争用。例如，将concurrent_insert系统变量为2，总是允许并发插入；同时，通过定期在系统空闲时段执行OPTIONMIZE TABLE语句来整理空间碎片，收到因删除记录而产生的中间空洞。

MyISAM的锁调度

MyISAM存储引擎的读和写锁是互斥，读操作是串行的。一个进程请求某个MyISAM表的读锁，同时另一个进程也请求同一表的写锁，MySQL如何处理呢？答案是写进程先获得锁。不仅如此，即使读进程先请求先到锁等待队列，写请求后到，写锁也会插到读请求之前！这是因为MySQL认为写请求一般比读请求重要。这也正是MyISAM表不太适合于有大量更新操作和查询操作应用的原因，因为，大量的更新操作会造成查询操作很难获得读锁，从而可能永远阻塞。这种情况有时可能会变得非常糟糕！幸好我们可以通过一些设置来调节MyISAM的调度行为。

- 通过指定启动参数low-priority-updates，使MyISAM引擎默认给予读请求以优先的权利。
- 通过执行命令SET LOW_PRIORITY_UPDATES=1，使该连接发出的更新请求优先级降低。
- 通过指定INSERT、UPDATE、DELETE语句的LOW_PRIORITY属性，降低该语句的优先级。

另外，MySQL也提供了一种折中的办法来调节读写冲突，即给系统参数max_write_lock_count设置一个合适的值，当一个表的读锁达到这个值后，MySQL变暂时将写请求的优先级降低，给读进程一定获得锁的机会。

------

InnoDB锁问题

InnoDB与MyISAM的最大不同有两点：一是支持事务；二是采用了行级锁。行级锁和表级锁本来就有许多不同之处，另外，事务的引入也带来了一些新问题。

事务（Transaction）及其ACID属性

事务是由一组SQL语句组成的逻辑处理单元，事务具有4属性，通常称为事务的ACID属性。

- 原性性（Actomicity）：事务是一个原子操作单元，其对数据的修改，要么全都执行，要么全都不执行。
- 一致性（Consistent）：在事务开始和完成时，数据都必须保持一致状态。这意味着所有相关的数据规则都必须应用于事务的修改，以操持完整性；事务结束时，所有的内部数据结构（如B树索引或双向链表）也都必须是正确的。
- 隔离性（Isolation）：数据库系统提供一定的隔离机制，保证事务在不受外部并发操作影响的“独立”环境执行。这意味着事务处理过程中的中间状态对外部是不可见的，反之亦然。
- 持久性（Durable）：事务完成之后，它对于数据的修改是永久性的，即使出现系统故障也能够保持。

并发事务带来的问题

相对于串行处理来说，并发事务处理能大大增加数据库资源的利用率，提高数据库系统的事务吞吐量，从而可以支持可以支持更多的用户。但并发事务处理也会带来一些问题，主要包括以下几种情况。

- 更新丢失（Lost Update）：当两个或多个事务选择同一行，然后基于最初选定的值更新该行时，由于每个事务都不知道其他事务的存在，就会发生丢失更新问题——最后的更新覆盖了其他事务所做的更新。例如，两个编辑人员制作了同一文档的电子副本。每个编辑人员独立地更改其副本，然后保存更改后的副本，这样就覆盖了原始文档。最后保存其更改保存其更改副本的编辑人员覆盖另一个编辑人员所做的修改。如果在一个编辑人员完成并提交事务之前，另一个编辑人员不能访问同一文件，则可避免此问题
- 脏读（Dirty Reads）：一个事务正在对一条记录做修改，在这个事务并提交前，这条记录的数据就处于不一致状态；这时，另一个事务也来读取同一条记录，如果不加控制，第二个事务读取了这些“脏”的数据，并据此做进一步的处理，就会产生未提交的数据依赖关系。这种现象被形象地叫做“脏读”。
- 不可重复读（Non-Repeatable Reads）：一个事务在读取某些数据已经发生了改变、或某些记录已经被删除了！这种现象叫做“不可重复读”。
- 幻读（Phantom Reads）：一个事务按相同的查询条件重新读取以前检索过的数据，却发现其他事务插入了满足其查询条件的新数据，这种现象就称为“幻读”。

事务隔离级别

1. 在并发事务处理带来的问题中，“更新丢失”通常应该是完全避免的。但防止更新丢失，并不能单靠数据库事务控制器来解决，需要应用程序对要更新的数据加必要的锁来解决，因此，防止更新丢失应该是应用的责任。
2. “脏读”、“不可重复读”和“幻读”，其实都是数据库读一致性问题，必须由数据库提供一定的事务隔离机制来解决。数据库实现事务隔离的方式，基本可以分为以下两种。

- .一种是在读取数据前，对其加锁，阻止其他事务对数据进行修改。
- 另一种是不用加任何锁，通过一定机制生成一个数据请求时间点的一致性数据快照（Snapshot），并用这个快照来提供一定级别（语句级或事务级）的一致性读取。从用户的角度，好像是数据库可以提供同一数据的多个版本，因此，这种技术叫做数据多版本并发控制（ＭultiVersion Concurrency Control，简称MVCC或MCC），也经常称为多版本数据库。

数据库的事务隔离级别越严格，并发副作用越小，但付出的代价也就越大，因为事务隔离实质上就是使事务在一定程度上“串行化”进行，这显然与“并发”是矛盾的，同时，不同的应用对读一致性和事务隔离程度的要求也是不同的，比如许多应用对“不可重复读”和“幻读”并不敏感，可能更关心数据并发访问的能力。

为了解决“隔离”与“并发”的矛盾，ISO/ANSI SQL92定义了４个事务隔离级别，每个级别的隔离程度不同，允许出现的副作用也不同，应用可以根据自己业务逻辑要求，通过选择不同的隔离级别来平衡＂隔离＂与＂并发＂的矛盾

|隔离级别/读数据一致性及允许的并发副作用|读数据一致性|脏读|不可重复读|幻读|

| ------------- |:-------------:|

|未提交读（Read uncommitted）|最低级别，只能保证不读取物理上损坏的数据|是|是|是|

|已提交度（Read committed）|语句级|否|是|是|

|可重复读（Repeatable read）|事务级|否|否是|

|可序列化（Serializable）|最高级别，事务级|否|否|否|

//查看Innodb行锁争用情况 show status like 'innodb_row_lock%' //如果发现争用比较严重，如Innodb_row_lock_waits和Innodb_row_lock_time_avg的值比较高 //通过查询information_schema相关表来查看锁情况 select * from innodb_locks select * from innodb_locks_waits //或者通过设置Innodb monitors来进一步观察发生锁冲突的表，数据行等，并分析锁争用的原因 show ENGINE innodb status //停止监视器 drop table innodb_monitor; //默认情况每15秒回向日志中记录监控的内容，如果长时间打开会导致.err文件变得非常巨大，所以确认原因后，要删除监控表关闭监视器，或者通过使用--console选项来启动服务器以关闭写日志功能 1234567891011

InnoDB的行锁模式及加锁方法

InnoDB实现了以下两种类型的行锁。

- 共享锁（S）：允许一个事务去读一行，阻止其他事务获得相同数据集的排他锁。
- 排他锁（X）：允许获取排他锁的事务更新数据，阻止其他事务取得相同的数据集共享读锁和排他写锁。另外，为了允许行锁和表锁共存，实现多粒度锁机制，InnoDB还有两种内部使用的意向锁（Intention Locks），这两种意向锁都是表锁。
- 意向共享锁（IS）：事务打算给数据行共享锁，事务在给一个数据行加共享锁前必须先取得该表的IS锁。
- 意向排他锁（IX）：事务打算给数据行加排他锁，事务在给一个数据行加排他锁前必须先取得该表的IX锁。

|当前锁模式/是否兼容/请求锁模式|X|IX|S|IS|

| ------------- |:-------------:|

|X| 冲突| 冲突| 冲突| 冲突|

|IX|冲突|兼容|冲突|兼容|

|S| 冲突| 冲突|兼容|兼容|

|IS|冲突|兼容|兼容|兼容|

如果一个事务请求的锁模式与当前的锁兼容，InnoDB就请求的锁授予该事务；反之，如果两者两者不兼容，该事务就要等待锁释放。意向锁是InnoDB自动加的，不需用户干预。对于UPDATE、DELETE和INSERT语句，InnoDB会自动给涉及及数据集排他锁（X）；对于普通SELECT语句，InnoDB不会任何锁；事务可以通过以下语句显示给记录集加共享锁或排锁。

- 共享锁（Ｓ）：SELECT * FROM table_name WHERE ... LOCK IN SHARE MODE
- 排他锁（X）：SELECT * FROM table_name WHERE ... FOR UPDATE

用SELECT .. IN SHARE MODE获得共享锁，主要用在需要数据依存关系时确认某行记录是否存在，并确保没有人对这个记录进行UPDATE或者DELETE操作。但是如果当前事务也需要对该记录进行更新操作，则很有可能造成死锁，对于锁定行记录后需要进行更新操作的应用，应该使用SELECT ... FOR UPDATE方式获取排他锁。

例子

Innodb共享锁的例子

| session_1                                                    | session_2                                                    |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| set autocommit=0,select * from actor where id =1             | set autocommit=0,select * from actor where id =1             |
| 当前seesion对id为1的记录加入share mode 共享锁 select * from actor where id =1 lock in share mode |                                                              |
|                                                              | 其他seesion仍然可以查询，并对该记录加入sharemode select * from actor where id =1 lock in share mode |
| 当前session对锁定的记录进行更新，等待锁 update。。。where id=1 |                                                              |
|                                                              | 当前session对锁定记录进行更新，则会导致死锁退出 update。。。where id=1 |
| 获得锁，更新成功                                             |                                                              |

排他锁例子

| session_1                                                    | session_2                                                    |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| set autocommit=0,select * from actor where id =1             | set autocommit=0,select * from actor where id =1             |
| 当前seesion对id为1的记录加入for update 共享锁 select * from actor where id =1 for update |                                                              |
|                                                              | 可查询该记录select * from actor where id =1,但是不能再记录共享锁，会等待获得锁select * from actor where id =1 for update |
| 更新后释放锁 update。。。 commit                             |                                                              |
|                                                              | 其他session，获得所，得到其他seesion提交的记录               |

Innodb行锁实现方式

InnoDb行锁是通过给索引上的索引项加锁来实现，如果没有索引，InnoDB将通过隐藏的聚簇索引来对记录加锁

- Record Locks:对索引项加锁
- Gap lock:对索引项之的“间隙“，第一天记录前的”间隙“，或最后一条记录后的”间隙“，加锁
- Next-key lock：前两种的组合，对记录及其前面的间隙枷锁

InnoDb 这中行锁，实现特点意味着：如果不通过索引条件检索数据，那么Innodb将对表的所有记录枷锁，实际效果和表锁一样

间隙锁（Next-Key锁）

SELECT * FROM emp WHERE empid > 100 FOR UPDATE //    是一个范围条件的检索，InnoDB不仅会对符合条件的empid值为101的记录加锁，也会对empid大于101（这些记录并不存在）的“间隙”加锁。 12

InnoDB使用间隙锁的目的，一方面是为了防止幻读，以满足相关隔离级别的要求，对于上面的例子，要是不使用间隙锁，如果其他事务插入了empid大于100的任何记录，那么本事务如果再次执行上述语句，就会发生幻读；另一方面，是为了满足其恢复和复制的需要。很显然，在使用范围条件检索并锁定记录时，InnoDB这种加锁机制会阻塞符合条件范围内键值的并发插入，这往往会造成严重的锁等待。因此，在实际开发中，尤其是并发插入比较多的应用，我们要尽量优化业务逻辑，尽量使用相等条件来访问更新数据，避免使用范围条件。

什么时候使用表锁

对于InnoDB表，在绝大部分情况下都应该使用行级锁，因为事务和行锁往往是我们之所以选择InnoDB表的理由。但在个别特殊事务中，也可以考虑使用表级锁。

- 第一种情况是：事务需要更新大部分或全部数据，表又比较大，如果使用默认的行锁，不仅这个事务执行效率低，而且可能造成其他事务长时间锁等待和锁冲突，这种情况下可以考虑使用表锁来提高该事务的执行速度。
- 第二种情况是：事务涉及多个表，比较复杂，很可能引起死锁，造成大量事务回滚。这种情况也可以考虑一次性锁定事务涉及的表，从而避免死锁、减少数据库因事务回滚带来的开销。

当然，应用中这两种事务不能太多，否则，就应该考虑使用ＭyISAＭ表。 在InnoDB下 ，使用表锁要注意以下两点。

1. 使用LOCK TALBES虽然可以给InnoDB加表级锁，但必须说明的是，表锁不是由InnoDB存储引擎层管理的，而是由其上一层ＭySQL Server负责的，仅当autocommit=0、innodb_table_lock=1（默认设置）时，InnoDB层才能知道MySQL加的表锁，ＭySQL Server才能感知InnoDB加的行锁，这种情况下，InnoDB才能自动识别涉及表级锁的死锁；否则，InnoDB将无法自动检测并处理这种死锁。
2. 在用LOCAK TABLES对InnoDB锁时要注意，要将AUTOCOMMIT设为0，否则ＭySQL不会给表加锁；事务结束前，不要用UNLOCAK TABLES释放表锁，因为UNLOCK TABLES会隐含地提交事务；COMMIT或ROLLBACK产不能释放用LOCAK TABLES加的表级锁，必须用UNLOCK TABLES释放表锁，正确的方式见如下语句。

// 例如，如果需要写表t1并从表t读，可以按如下做： SET AUTOCOMMIT=0; LOCAK TABLES t1 WRITE, t2 READ, ...; [do something with tables t1 and here]; COMMIT; UNLOCK TABLES; 12345678

关于死锁

ＭyISAM表锁是deadlock free的，这是因为ＭyISAM总是一次性获得所需的全部锁，要么全部满足，要么等待，因此不会出现死锁。但是在InnoDB中，除单个SQL组成的事务外，锁是逐步获得的，这就决定了InnoDB发生死锁是可能的。

发生死锁后，InnoDB一般都能自动检测到，并使一个事务释放锁并退回，另一个事务获得锁，继续完成事务。但在涉及外部锁，或涉及锁的情况下，InnoDB并不能完全自动检测到死锁，这需要通过设置锁等待超时参数innodb_lock_wait_timeout来解决。需要说明的是，这个参数并不是只用来解决死锁问题，在并发访问比较高的情况下，如果大量事务因无法立即获取所需的锁而挂起，会占用大量计算机资源，造成严重性能问题，甚至拖垮数据库。我们通过设置合适的锁等待超时阈值，可以避免这种情况发生。

通常来说，死锁都是应用设计的问题，通过调整业务流程、数据库对象设计、事务大小、以及访问数据库的SQL语句，绝大部分都可以避免。下面就通过实例来介绍几种死锁的常用方法。

1. 在应用中，如果不同的程序会并发存取多个表，应尽量约定以相同的顺序为访问表，这样可以大大降低产生死锁的机会。如果两个session访问两个表的顺序不同，发生死锁的机会就非常高！但如果以相同的顺序来访问，死锁就可能避免。
2. 在程序以批量方式处理数据的时候，如果事先对数据排序，保证每个线程按固定的顺序来处理记录，也可以大大降低死锁的可能。
3. 在事务中，如果要更新记录，应该直接申请足够级别的锁，即排他锁，而不应该先申请共享锁，更新时再申请排他锁，甚至死锁。
4. 在REPEATEABLE-READ隔离级别下，如果两个线程同时对相同条件记录用SELECT...ROR UPDATE加排他锁，在没有符合该记录情况下，两个线程都会加锁成功。程序发现记录尚不存在，就试图插入一条新记录，如果两个线程都这么做，就会出现死锁。这种情况下，将隔离级别改成READ COMMITTED，就可以避免问题。
5. 当隔离级别为READ COMMITED时，如果两个线程都先执行SELECT...FOR UPDATE，判断是否存在符合条件的记录，如果没有，就插入记录。此时，只有一个线程能插入成功，另一个线程会出现锁等待，当第１个线程提交后，第２个线程会因主键重出错，但虽然这个线程出错了，却会获得一个排他锁！这时如果有第３个线程又来申请排他锁，也会出现死锁。对于这种情况，可以直接做插入操作，然后再捕获主键重异常，或者在遇到主键重错误时，总是执行ROLLBACK释放获得的排他锁。

如果出现死锁，可以用SHOW INNODB STATUS命令来确定最后一个死锁产生的原因和改进措施。

锁总结

对于MyISAM的表锁，主要有以下几点

1. 共享读锁（S）之间是兼容的，但共享读锁（S）和排他写锁（X）之间，以及排他写锁之间（X）是互斥的，也就是说读和写是串行的。
2. 在一定条件下，ＭyISAM允许查询和插入并发执行，我们可以利用这一点来解决应用中对同一表和插入的锁争用问题。
3. ＭyISAM默认的锁调度机制是写优先，这并不一定适合所有应用，用户可以通过设置LOW_PRIPORITY_UPDATES参数，或在INSERT、UPDATE、DELETE语句中指定LOW_PRIORITY选项来调节读写锁的争用。
4. 由于表锁的锁定粒度大，读写之间又是串行的，因此，如果更新操作较多，ＭyISAM表可能会出现严重的锁等待，可以考虑采用InnoDB表来减少锁冲突。

对于InnoDB表，主要有以下几点

1. InnoDB的行销是基于索引实现的，如果不通过索引访问数据，InnoDB会使用表锁。
2. InnoDB间隙锁机制，以及InnoDB使用间隙锁的原因。
3. 在不同的隔离级别下，InnoDB的锁机制和一致性读策略不同。
4. ＭySQL的恢复和复制对InnoDB锁机制和一致性读策略也有较大影响。
5. 锁冲突甚至死锁很难完全避免。

在了解InnoDB的锁特性后，用户可以通过设计和SQL调整等措施减少锁冲突和死锁，包括：

- 尽量使用较低的隔离级别
- 精心设计索引，并尽量使用索引访问数据，使加锁更精确，从而减少锁冲突的机会。
- 选择合理的事务大小，小事务发生锁冲突的几率也更小。
- 给记录集显示加锁时，最好一次性请求足够级别的锁。比如要修改数据的话，最好直接申请排他锁，而不是先申请共享锁，修改时再请求排他锁，这样容易产生死锁。
- 不同的程序访问一组表时，应尽量约定以相同的顺序访问各表，对一个表而言，尽可能以固定的顺序存取表中的行。这样可以大减少死锁的机会。
- 尽量用相等条件访问数据，这样可以避免间隙锁对并发插入的影响。
- 不要申请超过实际需要的锁级别；除非必须，查询时不要显示加锁。
- 对于一些特定的事务，可以使用表锁来提高处理速度或减少死锁的可能。

------

参考文章

深入浅出MySQL数据库开发、优化与管理维护

[http://blog.csdn.net/kash_chen007/article/details/19757577](https://link.jianshu.com/?t=http://blog.csdn.net/kash_chen007/article/details/19757577)

[http://blog.csdn.net/ustczyy/article/details/20567871](https://link.jianshu.com/?t=http://blog.csdn.net/ustczyy/article/details/20567871)

[http://www.cnblogs.com/chenqionghe/p/4845693.html](https://link.jianshu.com/?t=http://www.cnblogs.com/chenqionghe/p/4845693.html)