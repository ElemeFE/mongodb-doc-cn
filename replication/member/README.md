# Replica Set Members

A_replica set_in MongoDB is a group of[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)processes that provide redundancy and high availability. The members of a replica set are:

[Primary](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-primary-member)

.

The primary receives all write operations.

[Secondaries](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-secondary-members)

.

Secondaries replicate operations from the primary to maintain an identical data set. Secondaries may have additional configurations for special usage profiles. For example, secondaries may be

[non-voting](https://docs.mongodb.com/manual/core/replica-set-elections/#replica-set-non-voting-members)

or

[priority 0](https://docs.mongodb.com/manual/core/replica-set-priority-0-member/#replica-set-secondary-only-members)

.

You can also maintain an[arbiter](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-arbiters)as part of a replica set. Arbiters do not keep a copy of the data. However, arbiters play a role in the elections that select a primary if the current primary is unavailable.

The minimum recommended configuration for a replica set is a three member replica set with three data-bearing members: one[primary](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-primary-member)and two[secondary](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-secondary-members)members. You may alternatively deploy a three member replica set with_two_data-bearing members: a[primary](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-primary-member), a[secondary](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-secondary-members), and an[arbiter](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-arbiters), but replica sets with at least three data-bearing members offer better redundancy.

Changed in version 3.0.0:A replica set can have up to[50 members](https://docs.mongodb.com/manual/release-notes/3.0/#replica-sets-max-members)but only 7 voting members.[\[1\]](https://docs.mongodb.com/manual/core/replica-set-members/#master-slave)In previous versions, replica sets can have up to 12 members.

## Primary

The primary is the only member in the replica set that receives write operations. MongoDB applies write operations on the[primary](https://docs.mongodb.com/manual/reference/glossary/#term-primary)and then records the operations on the primary’s[oplog](https://docs.mongodb.com/manual/core/replica-set-oplog/).[Secondary](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-secondary-members)members replicate this log and apply the operations to their data sets.

In the following three-member replica set, the primary accepts all write operations. Then the secondaries replicate the oplog to apply to their data sets.

![](https://docs.mongodb.com/manual/_images/replica-set-read-write-operations-primary.bakedsvg.svg "Diagram of default routing of reads and writes to the primary.")

All members of the replica set can accept read operations. However, by default, an application directs its read operations to the primary member. See[Read Preference](https://docs.mongodb.com/manual/core/read-preference/)for details on changing the default read behavior.

The replica set can have at most one primary.[\[2\]](https://docs.mongodb.com/manual/core/replica-set-members/#edge-cases-2-primaries)If the current primary becomes unavailable, an election determines the new primary. See[Replica Set Elections](https://docs.mongodb.com/manual/core/replica-set-elections/)for more details.

## Secondaries

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

## Arbiter

An arbiter does**not**have a copy of data set and**cannot**become a primary. Replica sets may have arbiters to add a vote in[elections for primary](https://docs.mongodb.com/manual/core/replica-set-elections/#replica-set-elections). Arbiters_always_have exactly`1`election vote, and thus allow replica sets to have an uneven number of voting members without the overhead of an additional member that replicates data.

IMPORTANT

Do not run an arbiter on systems that also host the primary or the secondary members of the replica set.

To add an arbiter, see[Add an Arbiter to Replica Set](https://docs.mongodb.com/manual/tutorial/add-replica-set-arbiter/).

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




