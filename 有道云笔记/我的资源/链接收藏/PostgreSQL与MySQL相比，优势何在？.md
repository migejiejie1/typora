PostgreSQL类似于Oracle的多进程框架，可以支持高并发的应用场景。如果把OracleDBA转到PostgreSQL数据库上是比较容易的，毕竟PostgreSQL数据库与Oracle数据库很相似。PostgreSQL几乎支持所有的SQL标准，支持类型相当丰富。PostgreSQL数据库的源代码要比MySQL数据库的源代码更容易读懂，如果团队的C语言能力比较强的话，就可以在PostgreSQL数据库上做开发，比方说实现类似greenplum的系统，这样也能与现在的分布式趋势接轨。为了说明PostgreSQL的功能，下面从“从Oracle迁移到Mysql之前必须知道的50件事”简要对比一下PostgreSQL数据库与MySQL数据库之间的差异。

从Oracle迁移到Mysql会面对的50件难事1、对子查询的优化表现不佳。（PostgreSQL可解决）2、对复杂查询的处理较弱。（PostgreSQL可解决）3、查询优化器不够成熟。（PostgreSQL可解决）PostgreSQL完全支持SQL-92标准，对SQL的支持也很全面，可以支持复杂的SQL查询。4、性能优化工具与度量信息不足。（PostgreSQL可解决）PostgreSQL提供了执行计划和详细的cost值，可以方便看到SQL的执行效率。5、审计功能相对较弱。6、安全功能不成熟，没有用户组与角色的概念，没有回收权限的功能(仅可以授予权限)。当一个用户从不同的主机/网络以同样的用户名/密码登录之后，可能被当做完全不同的用户来处理，没有类似于Oracle的内置的加密功能。

7、身份验证功能是完全内置的，不支持LDAP、ActiveDirectory或其它类似的外部身份验证功能。8、MysqlCluster可能与你想象的有较大差异。9、存储过程与触发器的功能有限。（PostgreSQL可解决）PostgreSQL提供了完善的存储过程和触发器支持。10、垂直扩展性较弱。11、不支持MPP(大规模并行处理)。（PostgreSQL可解决）PostgreSQL是类似Oracle数据库的多进程架构，而不像MySQL是多线程的架构，所以能支持MPP。12、支持SMP(对称多处理器),但是如果每个处理器超过4或8个核(core)时，Mysql的扩展性表现较差。13、对于时间、日期、间隔等时间类型没有秒以下级别的存储类型。

14、可用来编写存储过程、触发器、计划事件以及存储函数的语言功能较弱。15、没有基于回滚(roll-back)的恢复功能，只有前滚(roll-forward)的恢复功能。16、不支持快照功能。17、不支持数据库链(databaselink)。有一种叫做Federated的存储引擎可以作为一个中转将查询语句传递到远程服务器的一个表上，不过，它功能很粗糙并且漏洞很多。18、数据完整性检查非常薄弱，即使是基本的完整性约束，也往往不能执行。（PostgreSQL可解决）PostgreSQL提供完善的数据完整性检查机制，支持外键。19、优化查询语句执行计划的优化器提示非常少。20、只有一种表连接类型：嵌套循环连接(nested-loop),不支持排序-合并连接(sort-mergejoin)与散列连接(hashjoin)。

（PostgreSQL可解决）PostgreSQL则支持这些表连接类型。21、大部分查询只能使用表上的单一索引；在某些情况下，会存在使用多个索引的查询，但是查询优化器通常会低估其成本，它们常常比表扫描还要慢。（PostgreSQL可解决）PostgreSQL数据不存在这个问题，假设表T的两个字段col1的col2上有两个索引，idx_1和idx_2，那么select*fromtwherecol1=:aandcol2=:b;查询时，PostgreSQL数据库有可能把这个查询转化为select*fromtwherecol1=:aintersectselect*fromtwherecol2=:b，这样两个索引都可以使用上。22、不支持位图索引(bitmapindex)。

每种存储引擎都支持不同类型的索引。大部分存储引擎都支持B-Tree索引。23、管理工具较少，功能也不够成熟。24、没有成熟能够令人满意的IDE工具与调试程序。可能不得不在文本编辑器中编写存储过程，并且通过往表(调试日志表)中插入记录的方式来做调试。25、每个表都可以使用一种不同的存储引擎。（PostgreSQL可解决）26、每个存储引擎在行为表现、特性以及功能上都可能有很大差异。（PostgreSQL可解决）27、大部分存储引擎都不支持外键。（PostgreSQL可解决）28、默认的存储引擎(MyISAM)不支持事务，并且很容易损坏。（PostgreSQL可解决）29、最先进最流行的存储引擎InnoDB由Oracle拥有。

（PostgreSQL可解决）30、有些执行计划只支持特定的存储引擎。特定类型的Count查询，在这种存储引擎中执行很快，在另外一种存储引擎中可能会很慢。（PostgreSQL可解决）PostgreSQL只有一种存储引擎，所以不存在上面的情况。而PostgreSQL支持完善的事务。31、执行计划并不是全局共享的，,仅仅在连接内部是共享的。32、全文搜索功能有限，只适用于非事务性存储引擎。Ditto用于地理信息系统/空间类型和查询。（PostgreSQL可解决）PostgreSQL数据库支持全文搜索，支持更多类型的索引，如B-tree,R-tree,Hash,GiST,GIN，R-tree,GIST,GIN索引可用于空间类型和查询。

33、没有资源控制。一个完全未经授权的用户可以毫不费力地耗尽服务器的所有内存并使其崩溃，或者可以耗尽所有CPU资源。34、没有集成商业智能(businessintelligence),OLAP数据集等软件包。35、没有与GridControl类似的工具36、没有类似于RAC的功能。如果你问”如何使用Mysql来构造RAC”,只能说你问错了问题。37、不支持用户自定义类型或域(domain)。（PostgreSQL可解决）PostgreSQL支持丰富的类型，同时也支持自定义类型。38、每个查询支持的连接的数量最大为61。39、MySQL支持的SQL语法(ANSISQL标准)的很小一部分。不支持递归查询、通用表表达式（Oracle的with语句）或者窗口函数（分析函数）。

支持部分类似于Merge或者类似特性的SQL语法扩展，不过相对于Oracle来讲功能非常简单。（PostgreSQL可解决）这些PostgreSQL数据库都支持，如窗口函数。40、不支持功能列(基于计算或者表达式的列，Oracle11g开始支持计算列，以及早期版本就支持虚列(rownum,rowid))。41、不支持函数索引，只能创建基于具体列的索引。（PostgreSQL可解决）PostgreSQL支持函数索引。42、不支持物化视图。43、不同的存储引擎之间，统计信息差别很大，并且所有的存储引擎支持的统计信息都只支持简单的基数(cardinality)与一定范围内的记录数(rows-in-a-range)。换句话说，数据分布统计信息是有限的。

更新统计信息的机制也不多。44、没有内置的负载均衡与故障切换机制。45、复制(Replication)功能是异步的，并且有很大的局限性。例如，它是单线程的(single-threaded),因此一个处理能力更强的Slave的恢复速度也很难跟上处理能力相对较慢的Master。46、Cluster并不如想象的那么完美。或许我已经提过这一点，但是这一点值得再说一遍。47、数据字典(INFORMATION_SCHEMA)功能很有限，并且访问速度很慢(在繁忙的系统上还很容易发生崩溃)。48、不支持在线的AlterTable操作。49、不支持Sequence。（PostgreSQL可解决）PostgreSQL支持sequence。50、类似于ALTERTABLE或CREATETABLE一类的操作都是非事务性的。

它们会提交未提交的事务，并且不能回滚也不能做灾难恢复。Schame被保存在文件系统上，这一点与它使用的存储引擎无关。（PostgreSQL可解决）PostgreSQL不存在这个问题。每种数据库都有不同的应用场景PostgreSQL具备了更高的可靠性，对数据一致性、完整性的支持高于MySQL，因此PostgreSQL更加适合严格的企业应用场景，MySQL查询速度较快，更适合业务逻辑相对简单、数据可靠性要求较低的互联网场景。