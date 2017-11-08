# Replica Set Members

MongoDB 的 _复制集_ 是一组 [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)进程实例, 提供了冗余和高可用性. 复制集的成员是:

[主节点](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-primary-member).

&nbsp;&nbsp;&nbsp;&nbsp; 主节点接受所有的写操作.

[从节点](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-secondary-members).

&nbsp;&nbsp;&nbsp;&nbsp; 从节点复现主节点的操作维护与其相同的数据集. 从节点可以配置用户一些特别的用途. 例如, 从节点可以是 [不投票的](https://docs.mongodb.com/manual/core/replica-set-elections/#replica-set-non-voting-members) 或者 [priority 0](https://docs.mongodb.com/manual/core/replica-set-priority-0-member/#replica-set-secondary-only-members).

你也可以在副本集中维护一个[仲裁节点](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-arbiters). 仲裁节点不复制数据. 但是, 当前主节点不可用时, 可以参与新的主节点的选举.

最小的副本集推荐形式为三个数据承载节点: 一个[主节点](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-primary-member)与两个[从节点](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-secondary-members). 你也可以替换为 _2_ 个数据承载节点: 一个[主](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-primary-member), 一个[从](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-secondary-members), 和一个[仲裁](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-arbiters)节点, 但是至少有三个数据承载节点可以提供更好的冗余.

3.0.0 版本更新: 一个副本集可以最多 [50 个节点](https://docs.mongodb.com/manual/release-notes/3.0/#replica-sets-max-members) 但是至多 7 节点投票.[\[1\]](https://docs.mongodb.com/manual/core/replica-set-members/#master-slave) 之前的版本里, 副本集最多能有 12 个节点.

## 主节点

The primary is the only member in the replica set that receives write operations. MongoDB applies write operations on the[primary](https://docs.mongodb.com/manual/reference/glossary/#term-primary)and then records the operations on the primary’s[oplog](https://docs.mongodb.com/manual/core/replica-set-oplog/).[Secondary](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-secondary-members)members replicate this log and apply the operations to their data sets.

In the following three-member replica set, the primary accepts all write operations. Then the secondaries replicate the oplog to apply to their data sets.

![](https://docs.mongodb.com/manual/_images/replica-set-read-write-operations-primary.bakedsvg.svg "Diagram of default routing of reads and writes to the primary.")

All members of the replica set can accept read operations. However, by default, an application directs its read operations to the primary member. See[Read Preference](https://docs.mongodb.com/manual/core/read-preference/)for details on changing the default read behavior.

The replica set can have at most one primary.[\[2\]](https://docs.mongodb.com/manual/core/replica-set-members/#edge-cases-2-primaries)If the current primary becomes unavailable, an election determines the new primary. See[Replica Set Elections](https://docs.mongodb.com/manual/core/replica-set-elections/)for more details.

## 从节点

A secondary maintains a copy of the[primary’s](https://docs.mongodb.com/manual/reference/glossary/#term-primary)data set. To replicate data, a secondary applies operations from the primary’s[oplog](https://docs.mongodb.com/manual/core/replica-set-oplog/)to its own data set in an asynchronous process. A replica set can have one or more secondaries.

The following three-member replica set has two secondary members. The secondaries replicate the primary’s oplog and apply the operations to their data sets.

![](https://docs.mongodb.com/manual/_images/replica-set-primary-with-two-secondaries.bakedsvg.svg "Diagram of a 3 member replica set that consists of a primary and two secondaries.")

Although clients cannot write data to secondaries, clients can read data from secondary members. See[Read Preference](https://docs.mongodb.com/manual/core/read-preference/)for more information on how clients direct read operations to replica sets.

A secondary can become a primary. If the current primary becomes unavailable, the replica set holds an[election](https://docs.mongodb.com/manual/reference/glossary/#term-election)to choose which of the secondaries becomes the new primary.

See[Replica Set Elections](https://docs.mongodb.com/manual/core/replica-set-elections/)for more details.

You can configure a secondary member for a specific purpose. You can configure a secondary to:

* Prevent it from becoming a primary in an election, which allows it to reside in a secondary data center or to serve as a cold standby. See
  [Priority 0 Replica Set Members](https://docs.mongodb.com/manual/core/replica-set-priority-0-member/)
  .
* Prevent applications from reading from it, which allows it to run applications that require separation from normal traffic. See
  [Hidden Replica Set Members](https://docs.mongodb.com/manual/core/replica-set-hidden-member/)
  .
* Keep a running “historical” snapshot for use in recovery from certain errors, such as unintentionally deleted databases. See
  [Delayed Replica Set Members](https://docs.mongodb.com/manual/core/replica-set-delayed-member/)
  .

## 仲裁节点

An 仲裁节点 does**not**have a copy of data set and**cannot**become a primary. Replica sets may have arbiters to add a vote in[elections for primary](https://docs.mongodb.com/manual/core/replica-set-elections/#replica-set-elections). Arbiters_always_have exactly`1`election vote, and thus allow replica sets to have an uneven number of voting members without the overhead of an additional member that replicates data.

IMPORTANT

Do not run an 仲裁节点 on systems that also host the primary or the secondary members of the replica set.

To add an 仲裁节点, see[Add an 仲裁节点 to Replica Set](https://docs.mongodb.com/manual/tutorial/add-replica-set-arbiter/).

WARNING

In general, avoid deploying more than one arbiter per replica set.

For the following MongoDB versions,`pv1`increases the likelihood of[`w:1`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern.<number>)rollbacks compared to`pv0`for replica sets with arbiters:

* MongoDB 3.4.1
* MongoDB 3.4.0
* MongoDB 3.2.11 or earlier

See[Replica Set Protocol Versions](https://docs.mongodb.com/manual/reference/replica-set-protocol-versions/).

| [\[1\]](https://docs.mongodb.com/manual/core/replica-set-members/#id1) | While replica sets are the recommended solution for production, a replica set can support up to[`50members`](https://docs.mongodb.com/manual/reference/limits/#Number-of-Members-of-a-Replica-Set)in total. If your deployment requires more than 50 members, you’ll need to use[master-slave](https://docs.mongodb.com/manual/core/master-slave/)replication. However, master-slave replication lacks the automatic failover capabilities. |
| :--- | :--- |


| [\[2\]](https://docs.mongodb.com/manual/core/replica-set-members/#id2) | In[some circumstances](https://docs.mongodb.com/manual/reference/read-preference/#edge-cases), two nodes in a replica set may_transiently_believe that they are the primary, but at most, one of them will be able to complete writes with[`{w:"majority"}`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority")write concern. The node that can complete[`{w:"majority"}`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority")writes is the current primary, and the other node is a former primary that has not yet recognized its demotion, typically due to a[network partition](https://docs.mongodb.com/manual/reference/glossary/#term-network-partition). When this occurs, clients that connect to the former primary may observe stale data despite having requested read preference[`primary`](https://docs.mongodb.com/manual/reference/read-preference/#primary), and new writes to the former primary will eventually roll back. |
| :--- | :--- |




