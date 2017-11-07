# Hidden Replica Set Members

On this page

* [Behavior](https://docs.mongodb.com/manual/core/replica-set-hidden-member/#behavior)
* [Further Reading](https://docs.mongodb.com/manual/core/replica-set-hidden-member/#further-reading)

A hidden member maintains a copy of the[primary’s](https://docs.mongodb.com/manual/reference/glossary/#term-primary)data set but is**invisible**to client applications. Hidden members are good for workloads with different usage patterns from the other members in the[replica set](https://docs.mongodb.com/manual/reference/glossary/#term-replica-set). Hidden members must always be[priority 0 members](https://docs.mongodb.com/manual/core/replica-set-priority-0-member/#replica-set-secondary-only-members)and so**cannot become primary**. The[`db.isMaster()`](https://docs.mongodb.com/manual/reference/method/db.isMaster/#db.isMaster)method does not display hidden members. Hidden members, however,**may vote**in[elections](https://docs.mongodb.com/manual/core/replica-set-elections/#replica-set-elections).

In the following five-member replica set, all four secondary members have copies of the primary’s data set, but one of the secondary members is hidden.

![](https://docs.mongodb.com/manual/_images/replica-set-hidden-member.bakedsvg.svg "Diagram of a 5 member replica set with a hidden priority 0 member.")

## Behavior

### Read Operations

Clients will not distribute reads with the appropriate[read preference](https://docs.mongodb.com/manual/core/read-preference/)to hidden members. As a result, these members receive no traffic other than basic replication. Use hidden members for dedicated tasks such as reporting and backups.[Delayed members](https://docs.mongodb.com/manual/core/replica-set-delayed-member/)should be hidden.

In a sharded cluster,[`mongos`](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos)do not interact with hidden members.

### Voting

Hidden members_may_vote in replica set elections. If you stop a voting hidden member, ensure that the set has an active majority or the[primary](https://docs.mongodb.com/manual/reference/glossary/#term-primary)will step down.

For the purposes of backups,

* If using the MMAPv1 storage engine, you can avoid stopping a hidden member with the[`db.fsyncLock()`](https://docs.mongodb.com/manual/reference/method/db.fsyncLock/#db.fsyncLock)and[`db.fsyncUnlock()`](https://docs.mongodb.com/manual/reference/method/db.fsyncUnlock/#db.fsyncUnlock)operations to flush all writes and lock the[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)instance for the duration of the backup operation.

* Changed in version 3.2:[`db.fsyncLock()`](https://docs.mongodb.com/manual/reference/method/db.fsyncLock/#db.fsyncLock)can ensure that the data files do not change for MongoDB instances using either the MMAPv1 or the WiredTiger storage engines, thus providing consistency for the purposes of creating backups.

  In previous MongoDB versions,[`db.fsyncLock()`](https://docs.mongodb.com/manual/reference/method/db.fsyncLock/#db.fsyncLock)_cannot_guarantee a consistent set of files for low-level backups \(e.g. via file copy`cp`,`scp`,`tar`\) for WiredTiger.

## Further Reading

For more information about backing up MongoDB databases, see[MongoDB Backup Methods](https://docs.mongodb.com/manual/core/backups/). To configure a hidden member, see[Configure a Hidden Replica Set Member](https://docs.mongodb.com/manual/tutorial/configure-a-hidden-replica-set-member/).

