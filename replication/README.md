# Replication

本页索引

* [冗余和数据可用性](#冗余和数据可用性)
* [MongoDB 的复制](#mongodb-的复制)
* [异步复制](#异步复制)
* [自动熔断](#自动熔断)
* [读操作](#读操作)
* [附加特性](#附加特性)

MongoDB 的 _复制集_ 是一组维护同样数据的 [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)进程实例. 复制集提供了冗余和高可用性, 是所有生产部署的基础. 本节介绍 MongoDB 中的复制以及复制集的组件和体系结构. 本节还提供与副本集相关的常见任务的教程.

## 冗余和数据可用性

复制提供冗余并增加数据可用性. 在不同数据库服务器上有多个数据副本的情况下, 复制可以提供一定的容错能力, 以防止单个节点的数据丢失.

在某些情况下, 复制可以提高读取能力, 因为客户端可以将读取操作发送到不同的服务器. 在不同的数据中心中维护数据副本可以提高分布式应用程序的数据本地化和可用性. 您也可以为了专门目的而保留其他副本, 例如灾难恢复, 报告或备份.

## MongoDB 的复制

副本集是维护同样数据的一组 [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) 实例. 副本集包含多个数据承载节点和可选的一个仲裁节点. 在数据承载节点中, 只有一个成员被认为是主节点, 而其他节点被认为是从节点.

[主节点](https://docs.mongodb.com/manual/core/replica-set-primary/)接收所有的写操作. 一个副本集只能有一个主节点来确通过 [`{w:"majority"}`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern. "majority") 确认写操作; 尽管在某些情况下, 另一个 mongod 实例可能暂时认为自己也是主节点.  [\[1\]](#edge-cases-2-primaries) 主节点会将所有的数据变化记录在它的操作日志中, 即 [oplog](https://docs.mongodb.com/manual/core/replica-set-oplog/). 有关主节点操作的更多信息, 请参见 [Replica Set Primary](https://docs.mongodb.com/manual/core/replica-set-primary/).

![](https://docs.mongodb.com/manual/_images/replica-set-read-write-operations-primary.bakedsvg.svg "Diagram of default routing of reads and writes to the primary.")

从节点复制主节点的 oplog 然后根据该操作复现，使得从节点的数据集映射了主节点数据集。如果主节点不可用，合格的从节点将会进行选举推选自己成为新的主节点。有关从节点的更多信息，请参见 [Replica Set Secondary Members](https://docs.mongodb.com/manual/core/replica-set-secondary/).

![](https://docs.mongodb.com/manual/_images/replica-set-primary-with-two-secondaries.bakedsvg.svg "Diagram of a 3 member replica set that consists of a primary and two secondaries.")

您可以将一个额外的 [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) 实例作为仲裁节点添加到副本集。仲裁节点不维护数据集。仲裁节点的目的是通过响应其他节点的心跳和选举请求来维持副本集中的额定成员。因为它们不存储数据，所以仲裁节点可以成为提供副本集仲裁功能的好方法，其开销比具有数据集完全功能的成员节点要低。如果您的副本集有偶数个节点，请添加一个仲裁节点来确保选举能获得大多数支持的结果。仲裁节点不需要专门的机器。有关仲裁节点的更多信息，请参阅 [Replica Set Arbiter](https://docs.mongodb.com/manual/core/replica-set-arbiter/).

![](https://docs.mongodb.com/manual/_images/replica-set-primary-with-secondary-and-arbiter.bakedsvg.svg "Diagram of a replica set that consists of a primary, a secondary, and an arbiter.")

一个[仲裁节点](https://docs.mongodb.com/manual/core/replica-set-arbiter/)将永远是[仲裁节点](https://docs.mongodb.com/manual/core/replica-set-arbiter/), 而选举中[主节点](https://docs.mongodb.com/manual/core/replica-set-primary/)可降级并成为一个[从节点](https://docs.mongodb.com/manual/core/replica-set-secondary/), [从节点](https://docs.mongodb.com/manual/core/replica-set-secondary/)也可以成为[主节点](https://docs.mongodb.com/manual/core/replica-set-primary/)。

## 异步复制

从节点是异步的同步来自主节点的操作。通过复现主节点的操作之后，即使一个或多个节点挂了，复制集也可以继续工作。有关复制机制的更多信息，请参见[副本集 Oplog](https://docs.mongodb.com/manual/core/replica-set-oplog/#replica-set-oplog) 和  [副本集数据同步](https://docs.mongodb.com/manual/core/replica-set-sync/#replica-set-sync)。

## 自动熔断

当主节点超过 10 秒钟的时间不与其他成员沟通时，合格的从节点将举行选举，推举自己成为新的主节点。选举中获得大多数选票的从节点将晋升为主节点。

3.2 版本更新: MongoDB 引入了第 1 版本复制协议 \([`protocolVersion:1`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.protocolVersion)\) 来减少副本集的熔断时间并且加快检查出多个主节点并行情况的时间. 新的副本集会默认使用 [`protocolVersion:1`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.protocolVersion). 之前的 MongoDB 版本使用弟 0 版协议.

![](https://docs.mongodb.com/manual/_images/replica-set-trigger-election.bakedsvg.svg "Diagram of an election of a new primary. In a three member replica set with two secondaries, the primary becomes unreachable. The loss of a primary triggers an election where one of the secondaries becomes the new primary")

尽管时间可能不同，但故熔断过程通常会在一分钟内完成。例如，副本集的成员可能需要 10-30 秒才能确认[主节点](https://docs.mongodb.com/manual/reference/glossary/#term-primary) 不可用 \(详见 [`electionTimeoutMillis`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.settings.electionTimeoutMillis)\)。剩下的某个从节点[选举](https://docs.mongodb.com/manual/reference/glossary/#term-election)成为新的主节点。选举过程可能需要 10-30 秒。

3.2 版本更新: 从 MongoDB 3.2 开始, 通过[副本选举加强版](https://docs.mongodb.com/manual/release-notes/3.2/#rel-notes-rs-enhancements), MongoDB 降低了副本集熔断时间. 查看 [replication election enhancements](https://docs.mongodb.com/manual/release-notes/3.2/#rel-notes-rs-enhancements) 获取更多信息.

更多信息参见 [副本集选举](https://docs.mongodb.com/manual/core/replica-set-elections/#replica-set-elections) 和 [Rollbacks During Replica Set Failover](https://docs.mongodb.com/manual/core/replica-set-rollbacks/#replica-set-rollbacks).

## 读操作

默认情况下, 客户端从主节点 [\[1\]](#edge-cases-2-primaries) 读取数据; 然而, 客户端可用通过 [read preference](https://docs.mongodb.com/manual/core/read-preference/) 来指定对从节点进行读操作. 不过由于[异步复制](#asynchronous-replication) 的原因从节点读取数据存在延迟的情况 \(数据尚未同步到从节点\). 更多信息可以参见 [Read Preference](https://docs.mongodb.com/manual/core/read-preference/).

In MongoDB, clients can see the results of writes before the writes are [durable](https://docs.mongodb.com/manual/reference/glossary/#term-durable):

* Regardless of [write concern](https://docs.mongodb.com/manual/reference/write-concern/) , other clients using \[`"local"`\]\([https://docs.mongodb.com/manual/reference/read-concern/\#readconcern."local](https://docs.mongodb.com/manual/reference/read-concern/#readconcern."local)"\) \(i.e. the default\) readConcern can see the result of a write operation before the write operation is acknowledged to the issuing client.
* Clients using \[`"local"`\]\([https://docs.mongodb.com/manual/reference/read-concern/\#readconcern."local](https://docs.mongodb.com/manual/reference/read-concern/#readconcern."local)"\) \(i.e. the default\) readConcern can read data which may be subsequently [rolled back](https://docs.mongodb.com/manual/core/replica-set-rollbacks/) .

For more information on read isolations, consistency and recency for MongoDB, see [Read Isolation, Consistency, and Recency](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/).

## 附加特性

副本集提供了许多选项来满足应用的需求. 例如, 你可以通过[多个数据中心的节点](https://docs.mongodb.com/manual/core/replica-set-architecture-geographically-distributed/)来部署副本集, 或者调整一些节点的 [`members[n].priority`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.members[n].priority) 来控制选举结果. 副本集同时还支持设置专门的节点进行报告, 灾难恢复, 或者备份等功能.

更多信息参见 [Priority 0 副本集节点](https://docs.mongodb.com/manual/core/replica-set-priority-0-member/#replica-set-secondary-only-members), [隐藏副本集节点](https://docs.mongodb.com/manual/core/replica-set-hidden-member/#replica-set-hidden-members)和[延迟副本集节点](https://docs.mongodb.com/manual/core/replica-set-delayed-member/#replica-set-delayed-members).

\[1\]  _\(_[_1_](https://docs.mongodb.com/manual/replication/#id1)_,_[_2_](https://docs.mongodb.com/manual/replication/#id3)_\) _In [some circumstances](https://docs.mongodb.com/manual/reference/read-preference/#edge-cases), two nodes in a replica set may _transiently _believe that they are the primary, but at most, one of them will be able to complete writes with [`{w:"majority"}`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") write concern. The node that can complete [`{w:"majority"}`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority") writes is the current primary, and the other node is a former primary that has not yet recognized its demotion, typically due to a [network partition](https://docs.mongodb.com/manual/reference/glossary/#term-network-partition). When this occurs, clients that connect to the former primary may observe stale data despite having requested read preference[`primary`](https://docs.mongodb.com/manual/reference/read-preference/#primary), and new writes to the former primary will eventually roll back.

