**简介**

MySQL通过**复制（Replication）**实现存储系统的**高可用**。目前，MySQL支持的复制方式有：

- 异步复制（Asynchronous Replication）：原理最简单，性能最好。但是主备之间数据不一致的概率很大。
- 半同步复制（Semi-synchronous Replication）：相比异步复制，半同步复制牺牲了一定的性能，提升了主备之间数据的一致性（有一些情况还是会出现主备数据不一致）。
- 组复制（Group Replication）：基于Paxos算法实现分布式数据复制的强一致性。只要大多数机器存活就能保证系统可用。相比半同步复制，Group Replication的数据一致性和系统可用性更高。

本文主要讨论**MySQL半同步复制**。

**半同步复制的基本流程**

MySQL半同步复制的实现是建立在MySQL异步复制的基础上的。MySQL支持两种**略有不同**的半同步复制：AFTER_SYNC和AFTER_COMMIT（受[rpl_semi_sync_master_wait_wait_point](https://link.jianshu.com/?t=https://dev.mysql.com/doc/refman/5.7/en/replication-semisync.html)控制）。

开启半同步复制时，Master在返回之前会等待Slave的响应或超时。当Slave超时时，半同步复制退化成异步复制。这也是MySQL半同步复制存在的一个问题。**本文不讨论Salve超时的情形（不讨论异步复制）**。

**半同步复制AFTER_SYNC模式的基本流程**

AFTER_SYNC模式是MySQL 5.7才支持的半同步复制方式，也是MySQL5.7默认的半同步复制方式：

1. Prepare the transaction in the storage engine(s).
2. Write the transaction to the binlog, flush the binlog to disk.
3. **Wait for at least one slave to acknowledge the reception for the binlog events for the transaction.**
4. **Commit the transaction to the storage engine(s).**

**半同步复制AFTER_COMMIT模式的基本流程**

MySQL 5.5和5.6的半同步复制只支持AFTER_COMMIT：

1. Prepare the transaction in the storage engine(s).
2. Write the transaction to the binlog, flush the binlog to disk.
3. **Commit the transaction to the storage engine(s).**
4. **Wait for at least one slave to acknowledge the reception for the binlog events for the transaction.**

**AFTER_SYNC和AFTER_COMMIT两种方式的小结**

- AFTER_SYNC： 日志复制到Slave之后，Master再commit。

- - 所有在master上commit的事务都已经复制到slave。
  - 所有已经复制到slave的事务在master不一定commit了（比如，master将日志复制到slave之后，在commit之前宕机了）

- AFTER_COMMIT：Master commit之后再将日志复制到Slave。

- - 所有master上commit的事务不一定复制到slave。（比如，master commit之后，还没来得及将日志复制到slave就宕机了）
  - 所有已经复制到slave的事务在master上一定commit了。

- 很明显，AFTER_COMMIT在master宕机的情况下，无法保证数据的一致性（master commit之后，还没来得及将日志复制到slave就宕机了）。**本文接下来只讨论AFTER_SYNC模式。**
- MySQL5.7.3开始支持配置半同步复制等待Slave应答的个数：[rpl_semi_sync_master_wait_slave_count](https://link.jianshu.com/?t=http://my-replication-life.blogspot.com/2013/12/enforced-semi-synchronous-replication.html) 。

**AFTER_SYNC模式下的异常情况分析**

- 异常情况1：**master宕机后，主备切换**。

1. master执行事务T，在将事务T的binlog刷到硬盘之前，master发生宕机。slave升级为master。master重启后，crash recovery会对事务T进行回滚。主备数据一致。
2. master执行事务T，在将事务T的binlog刷到硬盘之后，收到slave的ACK之前，master发生宕机（存在pendinglog）。slave升级为master。

**2.1 slave还没有收到事务T的binlog，master重启后，crash recovery会直接提交pendinglog。主备数据不一致。**

2.2 slave已经收到事务T的binlog。主备数据一致。

- 异常情况2：**master宕机后，不切换主机。**只需考虑异常情况1中的2.1。

master重启后，直接提交pendinglog，此时，主备数据不一致：

1. slave连接上master，通过异步复制的方式获得事务T的binlog。主备数据一致。
2. slave还没来得及复制事务T的binlog，如果master又发生宕机，磁盘损坏。主备数据不一致，事务T的数据丢失。

**异常情况处理**

从上面异常情况的简单分析我们得知，半同步复制需要处理master宕机后重启存在pendinglog（slave没有应答的binlog）的特殊情况。

- 针对master宕机后，不进行主备切换的情形：

在crash recovery之后，master等到slave的连接和复制，直到至少有一个slave复制了所有已提交的事务的binlog。（SHOW MASTER STATUS on master and SELECT master_pos_wait() on slave）。

- 针对master宕机后，进行主备切换的情形：

旧master重启后，在crash recovery时，对pendinglog进行回滚。（人工截断master的binlog未复制的部分？）

**思考**

为什么master重启之后，crash recovery的过程中，是直接commit pendinglog，而不是重试请求slave的应答呢？

MySQL的异步复制和半同步复制都是由slave触发的，slave主动去连接master同步binlog。

1. 没有发生主备切换，机器重启后无法知道哪台机器是slave。
2. 如果发生主备切换，它已经不是master了，则不会再有slave连上来。如果继续等待，则无法正常运行。

**总结**

MySQL半同步复制存在以下问题：

1. 当Slave超时时，会退化成异步复制。
2. 当Master宕机时，数据一致性无法保证，需要人工处理。
3. 复制是串行的。

正因为MySQL在主备数据一致性存在着这些问题，影响了互联网业务7*24的高可用服务，因此各大公司纷纷祭出自己的“补丁”：腾讯的TDSQL、微信的PhxSQL、阿里的AliSQL、网易的InnoSQL。

MySQL官方已经在MySQL5.7推出新的复制模式——MySQL Group Replication。