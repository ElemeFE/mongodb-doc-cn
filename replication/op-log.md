# Replica Set Oplog

On this page

* [Oplog Size](https://docs.mongodb.com/manual/core/replica-set-oplog/#oplog-size)
* [Workloads that Might Require a Larger Oplog Size](https://docs.mongodb.com/manual/core/replica-set-oplog/#workloads-that-might-require-a-larger-oplog-size)
* [Oplog Status](https://docs.mongodb.com/manual/core/replica-set-oplog/#oplog-status)

The[oplog](https://docs.mongodb.com/manual/reference/glossary/#term-oplog)\(operations log\) is a special[capped collection](https://docs.mongodb.com/manual/reference/glossary/#term-capped-collection)that keeps a rolling record of all operations that modify the data stored in your databases. MongoDB applies database operations on the[primary](https://docs.mongodb.com/manual/reference/glossary/#term-primary)and then records the operations on the primary’s oplog. The[secondary](https://docs.mongodb.com/manual/reference/glossary/#term-secondary)members then copy and apply these operations in an asynchronous process. All replica set members contain a copy of the oplog, in the[`local.oplog.rs`](https://docs.mongodb.com/manual/reference/local-database/#local.oplog.rs)collection, which allows them to maintain the current state of the database.

To facilitate replication, all replica set members send heartbeats \(pings\) to all other members. Any member can import oplog entries from any other member.

Each operation in the oplog is[idempotent](https://docs.mongodb.com/manual/reference/glossary/#term-idempotent). That is, oplog operations produce the same results whether applied once or multiple times to the target dataset.

## Oplog Size

When you start a replica set member for the first time, MongoDB creates an oplog of a default size.

For Unix and Windows systems

The default oplog size depends on the storage engine:

| Storage Engine | Default Oplog Size | Lower Bound | Upper Bound |
| :--- | :--- | :--- | :--- |
| [In-Memory Storage Engine](https://docs.mongodb.com/manual/core/inmemory/) | 5% of physical memory | 50 MB | 50 GB |
| [WiredTiger Storage Engine](https://docs.mongodb.com/manual/core/wiredtiger/) | 5% of free disk space | 990 MB | 50 GB |
| [MMAPv1 Storage Engine](https://docs.mongodb.com/manual/core/mmapv1/) | 5% of free disk space | 990 MB | 50 GB |

For 64-bit macOS systems

The default oplog size is 192 MB of either physical memory or free disk space depending on the storage engine:

| Storage Engine | Default Oplog Size |
| :--- | :--- |
| [In-Memory Storage Engine](https://docs.mongodb.com/manual/core/inmemory/) | 192 MB of physical memory |
| [WiredTiger Storage Engine](https://docs.mongodb.com/manual/core/wiredtiger/) | 192 MB of free disk space |
| [MMAPv1 Storage Engine](https://docs.mongodb.com/manual/core/mmapv1/) | 192 MB of free disk space |

In most cases, the default oplog size is sufficient. For example, if an oplog is 5% of free disk space and fills up in 24 hours of operations, then secondaries can stop copying entries from the oplog for up to 24 hours without becoming too stale to continue replicating. However, most replica sets have much lower operation volumes, and their oplogs can hold much higher numbers of operations.

Before[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)creates an oplog, you can specify its size with the[`oplogSizeMB`](https://docs.mongodb.com/manual/reference/configuration-options/#replication.oplogSizeMB)option. However, after you have started a replica set member for the first time, you can only change the size of the oplog using the[Change the Size of the Oplog](https://docs.mongodb.com/manual/tutorial/change-oplog-size/)procedure.

## Workloads that Might Require a Larger Oplog Size

If you can predict your replica set’s workload to resemble one of the following patterns, then you might want to create an oplog that is larger than the default. Conversely, if your application predominantly performs reads with a minimal amount of write operations, a smaller oplog may be sufficient.

The following workloads might require a larger oplog size.

### Updates to Multiple Documents at Once

The oplog must translate multi-updates into individual operations in order to maintain[idempotency](https://docs.mongodb.com/manual/reference/glossary/#term-idempotent). This can use a great deal of oplog space without a corresponding increase in data size or disk use.

### Deletions Equal the Same Amount of Data as Inserts

If you delete roughly the same amount of data as you insert, the database will not grow significantly in disk use, but the size of the operation log can be quite large.

### Significant Number of In-Place Updates

If a significant portion of the workload is updates that do not increase the size of the documents, the database records a large number of operations but does not change the quantity of data on disk.

## Oplog Status

To view oplog status, including the size and the time range of operations, issue the[`rs.printReplicationInfo()`](https://docs.mongodb.com/manual/reference/method/rs.printReplicationInfo/#rs.printReplicationInfo)method. For more information on oplog status, see[Check the Size of the Oplog](https://docs.mongodb.com/manual/tutorial/troubleshoot-replica-sets/#replica-set-troubleshooting-check-oplog-size).

Under various exceptional situations, updates to a[secondary’s](https://docs.mongodb.com/manual/reference/glossary/#term-secondary)oplog might lag behind the desired performance time. Use[`db.getReplicationInfo()`](https://docs.mongodb.com/manual/reference/method/db.getReplicationInfo/#db.getReplicationInfo)from a secondary member and the[replication status](https://docs.mongodb.com/manual/reference/method/db.getReplicationInfo/)output to assess the current state of replication and determine if there is any unintended replication delay.

See[Replication Lag](https://docs.mongodb.com/manual/tutorial/troubleshoot-replica-sets/#replica-set-replication-lag)for more information.

