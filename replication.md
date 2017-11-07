# Replication

On this page

* [Redundancy and Data Availability](https://docs.mongodb.com/manual/replication/#redundancy-and-data-availability)
* [Replication in MongoDB](https://docs.mongodb.com/manual/replication/#replication-in-mongodb)
* [Asynchronous Replication](https://docs.mongodb.com/manual/replication/#asynchronous-replication)
* [Automatic Failover](https://docs.mongodb.com/manual/replication/#automatic-failover)
* [Read Operations](https://docs.mongodb.com/manual/replication/#read-operations)
* [Additional Features](https://docs.mongodb.com/manual/replication/#additional-features)

A_replica set_in MongoDB is a group of[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)processes that maintain the same data set. Replica sets provide redundancy and high availability, and are the basis for all production deployments. This section introduces replication in MongoDB as well as the components and architecture of replica sets. The section also provides tutorials for common tasks related to replica sets.

## Redundancy and Data Availability

Replication provides redundancy and increases data availability. With multiple copies of data on different database servers, replication provides a level of fault tolerance against the loss of a single database server.

In some cases, replication can provide increased read capacity as clients can send read operations to different servers. Maintaining copies of data in different data centers can increase data locality and availability for distributed applications. You can also maintain additional copies for dedicated purposes, such as disaster recovery, reporting, or backup.

## Replication in MongoDB

A replica set is a group of[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)instances that maintain the same data set. A replica set contains several data bearing nodes and optionally one arbiter node. Of the data bearing nodes, one and only one member is deemed the primary node, while the other nodes are deemed secondary nodes.

The[primary node](https://docs.mongodb.com/manual/core/replica-set-primary/)receives all write operations. A replica set can have only one primary capable of confirming writes with[`{w:"majority"}`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority")write concern; although in some circumstances, another mongod instance may transiently believe itself to also be primary.[\[1\]](https://docs.mongodb.com/manual/replication/#edge-cases-2-primaries)The primary records all changes to its data sets in its operation log, i.e.[oplog](https://docs.mongodb.com/manual/core/replica-set-oplog/). For more information on primary node operation, see[Replica Set Primary](https://docs.mongodb.com/manual/core/replica-set-primary/).

![](https://docs.mongodb.com/manual/_images/replica-set-read-write-operations-primary.bakedsvg.svg "Diagram of default routing of reads and writes to the primary.")

The[secondaries](https://docs.mongodb.com/manual/core/replica-set-secondary/)replicate the primary’s oplog and apply the operations to their data sets such that the secondaries’ data sets reflect the primary’s data set. If the primary is unavailable, an eligible secondary will hold an election to elect itself the new primary. For more information on secondary members, see[Replica Set Secondary Members](https://docs.mongodb.com/manual/core/replica-set-secondary/).

![](https://docs.mongodb.com/manual/_images/replica-set-primary-with-two-secondaries.bakedsvg.svg "Diagram of a 3 member replica set that consists of a primary and two secondaries.")

You may add an extra[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)instance to a replica set as an[arbiter](https://docs.mongodb.com/manual/core/replica-set-arbiter/). Arbiters do not maintain a data set. The purpose of an arbiter is to maintain a quorum in a replica set by responding to heartbeat and election requests by other replica set members. Because they do not store a data set, arbiters can be a good way to provide replica set quorum functionality with a cheaper resource cost than a fully functional replica set member with a data set. If your replica set has an even number of members, add an arbiter to obtain a majority of votes in an election for primary. Arbiters do not require dedicated hardware. For more information on arbiters, see[Replica Set Arbiter](https://docs.mongodb.com/manual/core/replica-set-arbiter/).

![](https://docs.mongodb.com/manual/_images/replica-set-primary-with-secondary-and-arbiter.bakedsvg.svg "Diagram of a replica set that consists of a primary, a secondary, and an arbiter.")

An[arbiter](https://docs.mongodb.com/manual/core/replica-set-arbiter/)will always be an arbiter whereas a[primary](https://docs.mongodb.com/manual/core/replica-set-primary/)may step down and become a[secondary](https://docs.mongodb.com/manual/core/replica-set-secondary/)and a[secondary](https://docs.mongodb.com/manual/core/replica-set-secondary/)may become the primary during an election.

## Asynchronous Replication

Secondaries apply operations from the primary asynchronously. By applying operations after the primary, sets can continue to function despite the failure of one or more members. For more information on replication mechanics, see[Replica Set Oplog](https://docs.mongodb.com/manual/core/replica-set-oplog/#replica-set-oplog)and[Replica Set Data Synchronization](https://docs.mongodb.com/manual/core/replica-set-sync/#replica-set-sync).

## Automatic Failover

When a primary does not communicate with the other members of the set for more than 10 seconds, an eligible secondary will hold an election to elect itself the new primary. The first secondary to hold an election and receive a majority of the members’ votes becomes primary.

New in version 3.2:MongoDB introduces a version 1 of the replication protocol \([`protocolVersion:1`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.protocolVersion)\) to reduce replica set failover time and accelerates the detection of multiple simultaneous primaries. New replica sets will, by default, use[`protocolVersion:1`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.protocolVersion). Previous versions of MongoDB use version 0 of the protocol.

![](https://docs.mongodb.com/manual/_images/replica-set-trigger-election.bakedsvg.svg "Diagram of an election of a new primary. In a three member replica set with two secondaries, the primary becomes unreachable. The loss of a primary triggers an election where one of the secondaries becomes the new primary")

Although the timing varies, the failover process generally completes within a minute. For instance, it may take 10-30 seconds for the members of a[replica set](https://docs.mongodb.com/manual/reference/glossary/#term-replica-set)to declare a[primary](https://docs.mongodb.com/manual/reference/glossary/#term-primary)inaccessible \(see[`electionTimeoutMillis`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.settings.electionTimeoutMillis)\). One of the remaining secondaries holds an[election](https://docs.mongodb.com/manual/reference/glossary/#term-election)to elect itself as a new primary. The election itself may take another 10-30 seconds.

Changed in version 3.2:Starting in MongoDB 3.2, with the[replication election enhancements](https://docs.mongodb.com/manual/release-notes/3.2/#rel-notes-rs-enhancements), MongoDB reduces replica set failover time. See[replication election enhancements](https://docs.mongodb.com/manual/release-notes/3.2/#rel-notes-rs-enhancements)for details.

See[Replica Set Elections](https://docs.mongodb.com/manual/core/replica-set-elections/#replica-set-elections)and[Rollbacks During Replica Set Failover](https://docs.mongodb.com/manual/core/replica-set-rollbacks/#replica-set-rollbacks)for more information.

## Read Operations

By default, clients read from the primary[\[1\]](https://docs.mongodb.com/manual/replication/#edge-cases-2-primaries); however, clients can specify a[read preference](https://docs.mongodb.com/manual/core/read-preference/)to send read operations to secondaries.[Asynchronous replication](https://docs.mongodb.com/manual/replication/#asynchronous-replication)to secondaries means that reads from secondaries may return data that does not reflect the state of the data on the primary. For information on reading from replica sets, see[Read Preference](https://docs.mongodb.com/manual/core/read-preference/).

In MongoDB, clients can see the results of writes before the writes are[durable](https://docs.mongodb.com/manual/reference/glossary/#term-durable):

* Regardless of
  [write concern](https://docs.mongodb.com/manual/reference/write-concern/)
  , other clients using
  [`"local"`](https://docs.mongodb.com/manual/reference/read-concern/#readconcern."local")
  \(i.e. the default\) readConcern can see the result of a write operation before the write operation is acknowledged to the issuing client.
* Clients using
  [`"local"`](https://docs.mongodb.com/manual/reference/read-concern/#readconcern."local")
  \(i.e. the default\) readConcern can read data which may be subsequently
  [rolled back](https://docs.mongodb.com/manual/core/replica-set-rollbacks/)
  .

For more information on read isolations, consistency and recency for MongoDB, see[Read Isolation, Consistency, and Recency](https://docs.mongodb.com/manual/core/read-isolation-consistency-recency/).

## Additional Features

Replica sets provide a number of options to support application needs. For example, you may deploy a replica set with[members in multiple data centers](https://docs.mongodb.com/manual/core/replica-set-architecture-geographically-distributed/), or control the outcome of elections by adjusting the[`members[n].priority`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.members[n].priority)of some members. Replica sets also support dedicated members for reporting, disaster recovery, or backup functions.

See[Priority 0 Replica Set Members](https://docs.mongodb.com/manual/core/replica-set-priority-0-member/#replica-set-secondary-only-members),[Hidden Replica Set Members](https://docs.mongodb.com/manual/core/replica-set-hidden-member/#replica-set-hidden-members)and[Delayed Replica Set Members](https://docs.mongodb.com/manual/core/replica-set-delayed-member/#replica-set-delayed-members)for more information.

| \[1\] | _\(_[_1_](https://docs.mongodb.com/manual/replication/#id1)_,_[_2_](https://docs.mongodb.com/manual/replication/#id3)_\)_In[some circumstances](https://docs.mongodb.com/manual/reference/read-preference/#edge-cases), two nodes in a replica set may_transiently_believe that they are the primary, but at most, one of them will be able to complete writes with[`{w:"majority"}`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority")write concern. The node that can complete[`{w:"majority"}`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority")writes is the current primary, and the other node is a former primary that has not yet recognized its demotion, typically due to a[network partition](https://docs.mongodb.com/manual/reference/glossary/#term-network-partition). When this occurs, clients that connect to the former primary may observe stale data despite having requested read preference[`primary`](https://docs.mongodb.com/manual/reference/read-preference/#primary), and new writes to the former primary will eventually roll back. |
| :--- | :--- |




