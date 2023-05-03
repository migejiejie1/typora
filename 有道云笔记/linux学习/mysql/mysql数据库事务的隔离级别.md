mysql数据库事务的隔离级别

[bloomingTony](https://www.zhihu.com/people/zhang-hai-tao-81-49)

bloomingtony.club

数据库的事务保证ACID特性，I指的就是isolation隔离性，数据库事务隔离性分四种级别，并且都是从读操作出发定义的，并通过数据库锁来实现，我们都知道数据库对并发要求很高的，如果锁粒度太大或者加锁太频繁，影响数据库性能，如果加锁粒度太小有无法保证事务隔离性，下面我们就来看下数据库的各种隔离级别以及如何通过锁来实现的。

数据库表初始数据状态：

​    ![0](https://app.yinxiang.com/FileSharing.action?hash=1/5cabe5b889e9bbd4f2f07660add734cd-57233)

**(1)未提交读 READ-UNCOMMITED**

未提交读隔离级别是最低的隔离级别，该级别不涉及到任何数据库锁，会产生脏读的情况，**脏读是指如果一个事务的更改还未提交，其他数据库的会话就读了该更改数据；**

事务1：

​    ![0](https://app.yinxiang.com/FileSharing.action?hash=1/ef2515eb605a09ac610ae340da24348d-139360)

事务2：

​    ![0](https://app.yinxiang.com/FileSharing.action?hash=1/72b1b555c08999265b0ebd8806d04f92-67956)

事务2 读到了事务1中未提交的数据；

**(2)已提交读 READ-COMMITED**

该隔离级别只能读到其他会话中已提交的数据，解决了脏读的问题，但是不能够重复读，**重复读是指在一个事务中，对于指定条件的数据视图始终是一致的，不管其他事务是否update/delete**

事务1：

​    ![0](https://app.yinxiang.com/FileSharing.action?hash=1/f8512e81b4b16bb774c5e60888ac79be-94370)

事务2：

​    ![0](https://app.yinxiang.com/FileSharing.action?hash=1/7ca7ddc6d7a2824af6ba31877805cf3d-112967)

事务1：

​    ![0](https://app.yinxiang.com/FileSharing.action?hash=1/e11b13c0052e3be135661c93648590c9-67139)

只有事务2中更改提交后，在事务1中方能看到更改后的数据；oracle、sqlserver默认的数据库隔离级别均是该级别

**(3)可重复读 REPEATABLE-COMMITED**

该隔离级别是mysql的默认隔离级别，不光解决脏读的问题，还解决了重复读的问题，即在一个事务中对于update和delete隔离；但是没解决幻读的问题；mysql的RR隔离级别是解决了幻读问题的，通过MVCC gap锁和读锁来实现的。

事务1：

​    ![0](https://app.yinxiang.com/FileSharing.action?hash=1/7fde4b4daf0e30a69143aeab9593d3a1-85428)

事务2：

​    ![0](https://app.yinxiang.com/FileSharing.action?hash=1/83e21d7e7ba3366c9164cc9a2a34f365-107049)

事务1：

​    ![0](https://app.yinxiang.com/FileSharing.action?hash=1/d23c21c00b2d84c2583f12f7eaf5fa19-57078)

由此可见，事务1在事务开始时数据就保持不变了，可以重复读取，并且视图一致；

**(4)串行化 Serializable**

**最高的隔离级别，**读用读锁，写用写锁，读锁和写锁互斥，这么做可以有效的避免幻读、不可重复读、脏读等问题，但会极大的降低数据库的并发能力。

**不可重复读和幻读的区别：**

很多人容易搞混不可重复读和幻读，确实这两者有些相似。但不可重复读重点在于update和delete，而幻读的重点在于insert。如果使用锁机制来实现这两种隔离级别，在可重复读中，该sql第一次读取到数据后，就将这些数据加锁，其它事务无法修改这些数据，就可以实现可重复读了。但这种方法却无法锁住insert的数据，所以当事务A先前读取了数据，或者修改了全部数据，事务B还是可以insert数据提交，这时事务A就会发现莫名其妙多了一条之前没有的数据，这就是幻读，不能通过行锁来避免。需要Serializable隔离级别 ，读用读锁，写用写锁，读锁和写锁互斥，这么做可以有效的避免幻读、不可重复读、脏读等问题，但会极大的降低数据库的并发能力。

Measure

Measure