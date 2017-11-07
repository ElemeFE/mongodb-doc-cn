# Replica Set Arbiter

On this page

* [Example](https://docs.mongodb.com/manual/core/replica-set-arbiter/#example)
* [Security](https://docs.mongodb.com/manual/core/replica-set-arbiter/#security)

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

## Example

For example, in the following replica set, an arbiter allows the set to have an odd number of votes for elections:

![](https://docs.mongodb.com/manual/_images/replica-set-four-members-add-arbiter.bakedsvg.svg "Diagram of a four member replica set plus an arbiter for odd number of votes.")

## Security

### Authentication

When running with[`authorization`](https://docs.mongodb.com/manual/reference/configuration-options/#security.authorization), arbiters exchange credentials with other members of the set to authenticate via`keyfiles`. MongoDB encrypts the authentication process. The MongoDB authentication exchange is cryptographically secure.

Because arbiters do not store data, they do not possess the internal table of user and role mappings used for authentication. Thus, the only way to log on to an arbiter with authorization active is to use the[localhost exception](https://docs.mongodb.com/manual/core/security-users/#localhost-exception).

### Communication

The only communication between arbiters and other set members are: votes during elections, heartbeats, and configuration data. These exchanges are not encrypted.

**However**, if your MongoDB deployment uses TLS/SSL, MongoDB will encrypt_all_communication between replica set members. See[Configure mongod and mongos for TLS/SSL](https://docs.mongodb.com/manual/tutorial/configure-ssl/)for more information.

As with all MongoDB components, run arbiters in trusted network environments.

