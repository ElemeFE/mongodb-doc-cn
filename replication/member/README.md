# Replica Set Members

MongoDB 的 _复制集_ 是一组 [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)进程实例, 提供了冗余和高可用性. 复制集的成员是:

[主节点](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-primary-member).

&nbsp;&nbsp;&nbsp;&nbsp; 主节点接受所有的写操作.

[从节点](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-secondary-members).

&nbsp;&nbsp;&nbsp;&nbsp; 从节点复现主节点的操作维护与其相同的数据集. 从节点可以配置用户一些特别的用途. 例如, 从节点可以是 [不投票的](https://docs.mongodb.com/manual/core/replica-set-elections/#replica-set-non-voting-members) 或者 [priority 0](https://docs.mongodb.com/manual/core/replica-set-priority-0-member/#replica-set-secondary-only-members).

你也可以在副本集中维护一个 [仲裁节点](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-arbiters). 仲裁节点不复制数据. 但是, 当前主节点不可用时, 可以参与新的主节点的选举.

最小的副本集推荐形式为三个数据承载节点: 一个 [主节点](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-primary-member)与两个 [从节点](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-secondary-members). 你也可以替换为 _2_ 个数据承载节点: 一个 [主](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-primary-member), 一个 [从](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-secondary-members), 和一个 [仲裁](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-arbiters)节点, 但是至少有三个数据承载节点可以提供更好的冗余.

3.0.0 版本更新: 一个副本集可以最多 [50 个节点](https://docs.mongodb.com/manual/release-notes/3.0/#replica-sets-max-members) 但是至多 7 节点投票.[\[1\]](https://docs.mongodb.com/manual/core/replica-set-members/#master-slave) 之前的版本里, 副本集最多能有 12 个节点.

## 主节点

主节点是副本集中唯一接受写操作的节点. MongoDB 在主节点上应用写操作然后记录操作到 [oplog](https://docs.mongodb.com/manual/core/replica-set-oplog/). [从节点](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-secondary-members) 复制这个 log 并且复现这些操作来同步数据.

下例中三个成员的副本集, 主节点接受所有的写操作. 从节点通过 oplog 来同步数据.

![](https://docs.mongodb.com/manual/_images/replica-set-read-write-operations-primary.bakedsvg.svg "Diagram of default routing of reads and writes to the primary.")

副本集中的所有节点都可以接受读操作. 不过, 默认情况下, 应用会直接从主节点来读取数据. 参见 [Read Preference](https://docs.mongodb.com/manual/core/read-preference/) 获取更多修改默认读行为的信息.

一个副本集最多有一个主节点.[\[2\]](https://docs.mongodb.com/manual/core/replica-set-members/#edge-cases-2-primaries) 如果当前主节点不可用, 通过选举来确认新的主节点. 更多信息参见 [Replica Set Elections](https://docs.mongodb.com/manual/core/replica-set-elections/).

## 从节点

一个从节点维持了[主节点](https://docs.mongodb.com/manual/reference/glossary/#term-primary)的数据集副本. 拷贝过程, 是从节点通过主节点的 [oplog](https://docs.mongodb.com/manual/core/replica-set-oplog/) 来复现操作实现的一个异步过程. 一个副本集可以有一个或者多个从节点.

下图中三个节点的副本集有两个从节点. 从节点通过主节点的 oplog 来同步数据.

![](https://docs.mongodb.com/manual/_images/replica-set-primary-with-two-secondaries.bakedsvg.svg "Diagram of a 3 member replica set that consists of a primary and two secondaries.")

尽管客户端不能通过从节点来写入数据, 但是可以进行读操作. 参见 [Read Preference](https://docs.mongodb.com/manual/core/read-preference/) 获取更多客户端如何从从库读取数据的信息.

一个从节点可以变成主节点. 如果当前主节点不可用, 副本集则会举行一场[选举](https://docs.mongodb.com/manual/reference/glossary/#term-election)来选择选择一个从库成为新的主节点 (参见 [Replica Set Elections](https://docs.mongodb.com/manual/core/replica-set-elections/).

你可以配置从节点用于特别的目的, 比如:

* Prevent it from becoming a primary in an election, which allows it to reside in a secondary data center or to serve as a cold standby. 参见 [Priority 0 节点](https://docs.mongodb.com/manual/core/replica-set-priority-0-member/).
* Prevent applications from reading from it, which allows it to run applications that require separation from normal traffic. 参见 [Hidden 节点](https://docs.mongodb.com/manual/core/replica-set-hidden-member/).
* Keep a running “historical” snapshot for use in recovery from certain errors, such as unintentionally deleted databases. 参见 [Delayed 节点](https://docs.mongodb.com/manual/core/replica-set-delayed-member/).

## 仲裁节点

仲裁节点 **不存储** 数据, 也 **不能** 成为主节点. 副本集可以通过仲裁节点来[头片](https://docs.mongodb.com/manual/core/replica-set-elections/#replica-set-elections). 仲裁节点 _永远_ 有一个 `1` 选举票, 从而使得副本集可以拥有基数张票, 可以避免引入新的节点来投票的开销.


> <div style="background-color: #fff2d5;">IMPORTANT <br>
> 不要在主节点或者从节点的系统上运行仲裁节点. <br>
> </div>


要添加仲裁节点, 参见 [Add an arbiter to Replica Set](https://docs.mongodb.com/manual/tutorial/add-replica-set-arbiter/).

> <div style="background-color: #fae6e5;">WARNING <br>
> 通常来说, 避免在每个副本集中部署超过一个仲裁节点. <br>
> </div>

对于以下 MongoDB 版本, 相比 `pv0` 中有仲裁节点的副本集而言 `pv1` 增加了 [`w:1`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern.<number>) 回滚的可能性:

* MongoDB 3.4.1
* MongoDB 3.4.0
* MongoDB 3.2.11 or earlier

更多参见 [Replica Set Protocol Versions](https://docs.mongodb.com/manual/reference/replica-set-protocol-versions/).

| [\[1\]](https://docs.mongodb.com/manual/core/replica-set-members/#id1) | While replica sets are the recommended solution for production, a replica set can support up to [`50members`](https://docs.mongodb.com/manual/reference/limits/#Number-of-Members-of-a-Replica-Set)in total. If your deployment requires more than 50 members, you’ll need to use [master-slave](https://docs.mongodb.com/manual/core/master-slave/)replication. However, master-slave replication lacks the automatic failover capabilities. |
| :--- | :--- |


| [\[2\]](https://docs.mongodb.com/manual/core/replica-set-members/#id2) | In [some circumstances](https://docs.mongodb.com/manual/reference/read-preference/#edge-cases), two nodes in a replica set may_transiently_believe that they are the primary, but at most, one of them will be able to complete writes with [`{w:"majority"}`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority")write concern. The node that can complete [`{w:"majority"}`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority")writes is the current primary, and the other node is a former primary that has not yet recognized its demotion, typically due to a [network partition](https://docs.mongodb.com/manual/reference/glossary/#term-network-partition). When this occurs, clients that connect to the former primary may observe stale data despite having requested read preference [`primary`](https://docs.mongodb.com/manual/reference/read-preference/#primary), and new writes to the former primary will eventually roll back. |
| :--- | :--- |




