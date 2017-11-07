# Replica Set Primary

The primary is the only member in the replica set that receives write operations. MongoDB applies write operations on the[primary](https://docs.mongodb.com/manual/reference/glossary/#term-primary)and then records the operations on the primary’s[oplog](https://docs.mongodb.com/manual/core/replica-set-oplog/).[Secondary](https://docs.mongodb.com/manual/core/replica-set-members/#replica-set-secondary-members)members replicate this log and apply the operations to their data sets.

In the following three-member replica set, the primary accepts all write operations. Then the secondaries replicate the oplog to apply to their data sets.

![](https://docs.mongodb.com/manual/_images/replica-set-read-write-operations-primary.bakedsvg.svg "Diagram of default routing of reads and writes to the primary.")

All members of the replica set can accept read operations. However, by default, an application directs its read operations to the primary member. See[Read Preference](https://docs.mongodb.com/manual/core/read-preference/)for details on changing the default read behavior.

The replica set can have at most one primary.[\[1\]](https://docs.mongodb.com/manual/core/replica-set-primary/#edge-cases-2-primaries)If the current primary becomes unavailable, an election determines the new primary. See[Replica Set Elections](https://docs.mongodb.com/manual/core/replica-set-elections/)for more details.

In the following 3-member replica set, the primary becomes unavailable. This triggers an election which selects one of the remaining secondaries as the new primary.

![](https://docs.mongodb.com/manual/_images/replica-set-trigger-election.bakedsvg.svg "Diagram of an election of a new primary. In a three member replica set with two secondaries, the primary becomes unreachable. The loss of a primary triggers an election where one of the secondaries becomes the new primary")

| [\[1\]](https://docs.mongodb.com/manual/core/replica-set-primary/#id1) | In[some circumstances](https://docs.mongodb.com/manual/reference/read-preference/#edge-cases), two nodes in a replica set may_transiently_believe that they are the primary, but at most, one of them will be able to complete writes with[`{w:"majority"}`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority")write concern. The node that can complete[`{w:"majority"}`](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority")writes is the current primary, and the other node is a former primary that has not yet recognized its demotion, typically due to a[network partition](https://docs.mongodb.com/manual/reference/glossary/#term-network-partition). When this occurs, clients that connect to the former primary may observe stale data despite having requested read preference[`primary`](https://docs.mongodb.com/manual/reference/read-preference/#primary), and new writes to the former primary will eventually roll back. |
| :--- | :--- |




