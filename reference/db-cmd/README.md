# Database Commands

On this page

* [User Commands](https://docs.mongodb.com/manual/reference/command/#user-commands)
* [Database Operations](https://docs.mongodb.com/manual/reference/command/#database-operations)
* [Auditing Commands](https://docs.mongodb.com/manual/reference/command/#auditing-commands)

All command documentation outlined below describes a command and its available parameters and provides a document template or prototype for each command. Some command documentation also includes the relevant[`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)shell helpers.

To run a command against the current database, use[`db.runCommand()`](https://docs.mongodb.com/manual/reference/method/db.runCommand/#db.runCommand):

```
db
.
runCommand
(
{
<
command
>
}
)
```

To run an administrative command against the`admin`database, use[`db.adminCommand()`](https://docs.mongodb.com/manual/reference/method/db.adminCommand/#db.adminCommand):

```
db
.
adminCommand
(
{
<
command
>
}
)
```

NOTE

For details on specific commands, including syntax and examples, click on the specific command to go to its reference page.

## User Commands

### Aggregation Commands

| Name | Description |
| :--- | :--- |
| [`aggregate`](https://docs.mongodb.com/manual/reference/command/aggregate/#dbcmd.aggregate) | Performs[aggregation tasks](https://docs.mongodb.com/manual/core/aggregation-pipeline/)such as group using the aggregation framework. |
| [`count`](https://docs.mongodb.com/manual/reference/command/count/#dbcmd.count) | Counts the number of documents in a collection or a view. |
| [`distinct`](https://docs.mongodb.com/manual/reference/command/distinct/#dbcmd.distinct) | Displays the distinct values found for a specified key in a collection or a view. |
| [`group`](https://docs.mongodb.com/manual/reference/command/group/#dbcmd.group) | _Deprecated_. Groups documents in a collection by the specified key and performs simple aggregation. |
| [`mapReduce`](https://docs.mongodb.com/manual/reference/command/mapReduce/#dbcmd.mapReduce) | Performs[map-reduce](https://docs.mongodb.com/manual/core/map-reduce/)aggregation for large data sets. |

### Geospatial Commands

| Name | Description |
| :--- | :--- |
| [`geoNear`](https://docs.mongodb.com/manual/reference/command/geoNear/#dbcmd.geoNear) | Performs a geospatial query that returns the documents closest to a given point. |
| [`geoSearch`](https://docs.mongodb.com/manual/reference/command/geoSearch/#dbcmd.geoSearch) | Performs a geospatial query that uses MongoDB’s[haystack index](https://docs.mongodb.com/manual/reference/glossary/#term-haystack-index)functionality. |

### Query and Write Operation Commands

| Name | Description |
| :--- | :--- |
| [`delete`](https://docs.mongodb.com/manual/reference/command/delete/#dbcmd.delete) | Deletes one or more documents. |
| [`eval`](https://docs.mongodb.com/manual/reference/command/eval/#dbcmd.eval) | Deprecated. Runs a JavaScript function on the database server. |
| [`find`](https://docs.mongodb.com/manual/reference/command/find/#dbcmd.find) | Selects documents in a collection or a view. |
| [`findAndModify`](https://docs.mongodb.com/manual/reference/command/findAndModify/#dbcmd.findAndModify) | Returns and modifies a single document. |
| [`getLastError`](https://docs.mongodb.com/manual/reference/command/getLastError/#dbcmd.getLastError) | Returns the success status of the last operation. |
| [`getMore`](https://docs.mongodb.com/manual/reference/command/getMore/#dbcmd.getMore) | Returns batches of documents currently pointed to by the cursor. |
| [`getPrevError`](https://docs.mongodb.com/manual/reference/command/getPrevError/#dbcmd.getPrevError) | Returns status document containing all errors since the last[`resetError`](https://docs.mongodb.com/manual/reference/command/resetError/#dbcmd.resetError)command. |
| [`insert`](https://docs.mongodb.com/manual/reference/command/insert/#dbcmd.insert) | Inserts one or more documents. |
| [`parallelCollectionScan`](https://docs.mongodb.com/manual/reference/command/parallelCollectionScan/#dbcmd.parallelCollectionScan) | Lets applications use multiple parallel cursors when reading documents from a collection. |
| [`resetError`](https://docs.mongodb.com/manual/reference/command/resetError/#dbcmd.resetError) | Resets the last error status. |
| [`update`](https://docs.mongodb.com/manual/reference/command/update/#dbcmd.update) | Updates one or more documents. |

### Query Plan Cache Commands

| Name | Description |
| :--- | :--- |
| [`planCacheClear`](https://docs.mongodb.com/manual/reference/command/planCacheClear/#dbcmd.planCacheClear) | Removes cached query plan\(s\) for a collection. |
| [`planCacheClearFilters`](https://docs.mongodb.com/manual/reference/command/planCacheClearFilters/#dbcmd.planCacheClearFilters) | Clears index filter\(s\) for a collection. |
| [`planCacheListFilters`](https://docs.mongodb.com/manual/reference/command/planCacheListFilters/#dbcmd.planCacheListFilters) | Lists the index filters for a collection. |
| [`planCacheListPlans`](https://docs.mongodb.com/manual/reference/command/planCacheListPlans/#dbcmd.planCacheListPlans) | Displays the cached query plans for the specified query shape. |
| [`planCacheListQueryShapes`](https://docs.mongodb.com/manual/reference/command/planCacheListQueryShapes/#dbcmd.planCacheListQueryShapes) | Displays the query shapes for which cached query plans exist. |
| [`planCacheSetFilter`](https://docs.mongodb.com/manual/reference/command/planCacheSetFilter/#dbcmd.planCacheSetFilter) | Sets an index filter for a collection. |

## Database Operations

### Authentication Commands

| Name | Description |
| :--- | :--- |
| [`authenticate`](https://docs.mongodb.com/manual/reference/command/authenticate/#dbcmd.authenticate) | Starts an authenticated session using a username and password. |
| [`authSchemaUpgrade`](https://docs.mongodb.com/manual/reference/command/authSchemaUpgrade/#dbcmd.authSchemaUpgrade) | Supports the upgrade process for user data between version 2.4 and 2.6. |
| [`copydbgetnonce`](https://docs.mongodb.com/manual/reference/command/copydbgetnonce/#dbcmd.copydbgetnonce) | This is an internal command to generate a one-time password for use with the[`copydb`](https://docs.mongodb.com/manual/reference/command/copydb/#dbcmd.copydb)command. |
| [`getnonce`](https://docs.mongodb.com/manual/reference/command/getnonce/#dbcmd.getnonce) | This is an internal command to generate a one-time password for authentication. |
| [`logout`](https://docs.mongodb.com/manual/reference/command/logout/#dbcmd.logout) | Terminates the current authenticated session. |

### User Management Commands

| Name | Description |
| :--- | :--- |
| [`createUser`](https://docs.mongodb.com/manual/reference/command/createUser/#dbcmd.createUser) | Creates a new user. |
| [`dropAllUsersFromDatabase`](https://docs.mongodb.com/manual/reference/command/dropAllUsersFromDatabase/#dbcmd.dropAllUsersFromDatabase) | Deletes all users associated with a database. |
| [`dropUser`](https://docs.mongodb.com/manual/reference/command/dropUser/#dbcmd.dropUser) | Removes a single user. |
| [`grantRolesToUser`](https://docs.mongodb.com/manual/reference/command/grantRolesToUser/#dbcmd.grantRolesToUser) | Grants a role and its privileges to a user. |
| [`revokeRolesFromUser`](https://docs.mongodb.com/manual/reference/command/revokeRolesFromUser/#dbcmd.revokeRolesFromUser) | Removes a role from a user. |
| [`updateUser`](https://docs.mongodb.com/manual/reference/command/updateUser/#dbcmd.updateUser) | Updates a user’s data. |
| [`usersInfo`](https://docs.mongodb.com/manual/reference/command/usersInfo/#dbcmd.usersInfo) | Returns information about the specified users. |

### Role Management Commands

| Name | Description |
| :--- | :--- |
| [`createRole`](https://docs.mongodb.com/manual/reference/command/createRole/#dbcmd.createRole) | Creates a role and specifies its privileges. |
| [`dropRole`](https://docs.mongodb.com/manual/reference/command/dropRole/#dbcmd.dropRole) | Deletes the user-defined role. |
| [`dropAllRolesFromDatabase`](https://docs.mongodb.com/manual/reference/command/dropAllRolesFromDatabase/#dbcmd.dropAllRolesFromDatabase) | Deletes all user-defined roles from a database. |
| [`grantPrivilegesToRole`](https://docs.mongodb.com/manual/reference/command/grantPrivilegesToRole/#dbcmd.grantPrivilegesToRole) | Assigns privileges to a user-defined role. |
| [`grantRolesToRole`](https://docs.mongodb.com/manual/reference/command/grantRolesToRole/#dbcmd.grantRolesToRole) | Specifies roles from which a user-defined role inherits privileges. |
| [`invalidateUserCache`](https://docs.mongodb.com/manual/reference/command/invalidateUserCache/#dbcmd.invalidateUserCache) | Flushes the in-memory cache of user information, including credentials and roles. |
| [`revokePrivilegesFromRole`](https://docs.mongodb.com/manual/reference/command/revokePrivilegesFromRole/#dbcmd.revokePrivilegesFromRole) | Removes the specified privileges from a user-defined role. |
| [`revokeRolesFromRole`](https://docs.mongodb.com/manual/reference/command/revokeRolesFromRole/#dbcmd.revokeRolesFromRole) | Removes specified inherited roles from a user-defined role. |
| [`rolesInfo`](https://docs.mongodb.com/manual/reference/command/rolesInfo/#dbcmd.rolesInfo) | Returns information for the specified role or roles. |
| [`updateRole`](https://docs.mongodb.com/manual/reference/command/updateRole/#dbcmd.updateRole) | Updates a user-defined role. |

### Replication Commands

| Name | Description |
| :--- | :--- |
| [`applyOps`](https://docs.mongodb.com/manual/reference/command/applyOps/#dbcmd.applyOps) | Internal command that applies[oplog](https://docs.mongodb.com/manual/reference/glossary/#term-oplog)entries to the current data set. |
| [`isMaster`](https://docs.mongodb.com/manual/reference/command/isMaster/#dbcmd.isMaster) | Displays information about this member’s role in the replica set, including whether it is the master. |
| [`replSetFreeze`](https://docs.mongodb.com/manual/reference/command/replSetFreeze/#dbcmd.replSetFreeze) | Prevents the current member from seeking election as[primary](https://docs.mongodb.com/manual/reference/glossary/#term-primary)for a period of time. |
| [`replSetGetConfig`](https://docs.mongodb.com/manual/reference/command/replSetGetConfig/#dbcmd.replSetGetConfig) | Returns the replica set’s configuration object. |
| [`replSetGetStatus`](https://docs.mongodb.com/manual/reference/command/replSetGetStatus/#dbcmd.replSetGetStatus) | Returns a document that reports on the status of the replica set. |
| [`replSetInitiate`](https://docs.mongodb.com/manual/reference/command/replSetInitiate/#dbcmd.replSetInitiate) | Initializes a new replica set. |
| [`replSetMaintenance`](https://docs.mongodb.com/manual/reference/command/replSetMaintenance/#dbcmd.replSetMaintenance) | Enables or disables a maintenance mode, which puts a[secondary](https://docs.mongodb.com/manual/reference/glossary/#term-secondary)node in a`RECOVERING`state. |
| [`replSetReconfig`](https://docs.mongodb.com/manual/reference/command/replSetReconfig/#dbcmd.replSetReconfig) | Applies a new configuration to an existing replica set. |
| [`replSetStepDown`](https://docs.mongodb.com/manual/reference/command/replSetStepDown/#dbcmd.replSetStepDown) | Forces the current[primary](https://docs.mongodb.com/manual/reference/glossary/#term-primary)to\_step down\_and become a[secondary](https://docs.mongodb.com/manual/reference/glossary/#term-secondary), forcing an election. |
| [`replSetSyncFrom`](https://docs.mongodb.com/manual/reference/command/replSetSyncFrom/#dbcmd.replSetSyncFrom) | Explicitly override the default logic for selecting a member to replicate from. |
| [`resync`](https://docs.mongodb.com/manual/reference/command/resync/#dbcmd.resync) | Forces a[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)to re-synchronize from the[master](https://docs.mongodb.com/manual/reference/glossary/#term-master). For master-slave replication only. |

SEE ALSO

[Replication](https://docs.mongodb.com/manual/replication/)for more information regarding replication.

### Sharding Commands

| Name | Description |
| :--- | :--- |
| [`addShard`](https://docs.mongodb.com/manual/reference/command/addShard/#dbcmd.addShard) | Adds a[shard](https://docs.mongodb.com/manual/reference/glossary/#term-shard)to a[sharded cluster](https://docs.mongodb.com/manual/reference/glossary/#term-sharded-cluster). |
| [`addShardToZone`](https://docs.mongodb.com/manual/reference/command/addShardToZone/#dbcmd.addShardToZone) | Associates a shard with a[zone](https://docs.mongodb.com/manual/reference/glossary/#term-zone). Supports configuring[zones](https://docs.mongodb.com/manual/core/zone-sharding/#zone-sharding)in sharded clusters. |
| [`balancerStart`](https://docs.mongodb.com/manual/reference/command/balancerStart/#dbcmd.balancerStart) | Starts a balancer thread. |
| [`balancerStatus`](https://docs.mongodb.com/manual/reference/command/balancerStatus/#dbcmd.balancerStatus) | Returns information on the balancer status. |
| [`balancerStop`](https://docs.mongodb.com/manual/reference/command/balancerStop/#dbcmd.balancerStop) | Stops the balancer thread. |
| [`checkShardingIndex`](https://docs.mongodb.com/manual/reference/command/checkShardingIndex/#dbcmd.checkShardingIndex) | Internal command that validates index on shard key. |
| [`cleanupOrphaned`](https://docs.mongodb.com/manual/reference/command/cleanupOrphaned/#dbcmd.cleanupOrphaned) | Removes orphaned data with shard key values outside of the ranges of the chunks owned by a shard. |
| [`enableSharding`](https://docs.mongodb.com/manual/reference/command/enableSharding/#dbcmd.enableSharding) | Enables sharding on a specific database. |
| [`flushRouterConfig`](https://docs.mongodb.com/manual/reference/command/flushRouterConfig/#dbcmd.flushRouterConfig) | Forces an update to the cluster metadata cached by a[`mongos`](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos). |
| [`getShardMap`](https://docs.mongodb.com/manual/reference/command/getShardMap/#dbcmd.getShardMap) | Internal command that reports on the state of a sharded cluster. |
| [`getShardVersion`](https://docs.mongodb.com/manual/reference/command/getShardVersion/#dbcmd.getShardVersion) | Internal command that returns the[config server](https://docs.mongodb.com/manual/reference/glossary/#term-config-database)version. |
| [`isdbgrid`](https://docs.mongodb.com/manual/reference/command/isdbgrid/#dbcmd.isdbgrid) | Verifies that a process is a[`mongos`](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos). |
| [`listShards`](https://docs.mongodb.com/manual/reference/command/listShards/#dbcmd.listShards) | Returns a list of configured shards. |
| [`medianKey`](https://docs.mongodb.com/manual/reference/command/medianKey/#dbcmd.medianKey) | Deprecated internal command. See[`splitVector`](https://docs.mongodb.com/manual/reference/command/splitVector/#dbcmd.splitVector). |
| [`moveChunk`](https://docs.mongodb.com/manual/reference/command/moveChunk/#dbcmd.moveChunk) | Internal command that migrates chunks between shards. |
| [`movePrimary`](https://docs.mongodb.com/manual/reference/command/movePrimary/#dbcmd.movePrimary) | Reassigns the[primary shard](https://docs.mongodb.com/manual/reference/glossary/#term-primary-shard)when removing a shard from a sharded cluster. |
| [`mergeChunks`](https://docs.mongodb.com/manual/reference/command/mergeChunks/#dbcmd.mergeChunks) | Provides the ability to combine chunks on a single shard. |
| [`removeShard`](https://docs.mongodb.com/manual/reference/command/removeShard/#dbcmd.removeShard) | Starts the process of removing a shard from a sharded cluster. |
| [`removeShardFromZone`](https://docs.mongodb.com/manual/reference/command/removeShardFromZone/#dbcmd.removeShardFromZone) | Removes the association between a shard and a[zone](https://docs.mongodb.com/manual/reference/glossary/#term-zone). Supports configuring[zones](https://docs.mongodb.com/manual/core/zone-sharding/#zone-sharding)in sharded clusters. |
| [`setShardVersion`](https://docs.mongodb.com/manual/reference/command/setShardVersion/#dbcmd.setShardVersion) | Internal command to sets the[config server](https://docs.mongodb.com/manual/reference/glossary/#term-config-database)version. |
| [`shardCollection`](https://docs.mongodb.com/manual/reference/command/shardCollection/#dbcmd.shardCollection) | Enables the sharding functionality for a collection, allowing the collection to be sharded. |
| [`shardingState`](https://docs.mongodb.com/manual/reference/command/shardingState/#dbcmd.shardingState) | Reports whether the[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)is a member of a sharded cluster. |
| [`split`](https://docs.mongodb.com/manual/reference/command/split/#dbcmd.split) | Creates a new[chunk](https://docs.mongodb.com/manual/reference/glossary/#term-chunk). |
| [`splitChunk`](https://docs.mongodb.com/manual/reference/command/splitChunk/#dbcmd.splitChunk) | Internal command to split chunk. Instead use the methods[`sh.splitFind()`](https://docs.mongodb.com/manual/reference/method/sh.splitFind/#sh.splitFind)and[`sh.splitAt()`](https://docs.mongodb.com/manual/reference/method/sh.splitAt/#sh.splitAt). |
| [`splitVector`](https://docs.mongodb.com/manual/reference/command/splitVector/#dbcmd.splitVector) | Internal command that determines split points. |
| [`unsetSharding`](https://docs.mongodb.com/manual/reference/command/unsetSharding/#dbcmd.unsetSharding) | Internal command that affects connections between instances in a MongoDB deployment. |
| [`updateZoneKeyRange`](https://docs.mongodb.com/manual/reference/command/updateZoneKeyRange/#dbcmd.updateZoneKeyRange) | Adds or removes the association between a range of sharded data and a[zone](https://docs.mongodb.com/manual/reference/glossary/#term-zone). Supports configuring[zones](https://docs.mongodb.com/manual/core/zone-sharding/#zone-sharding)in sharded clusters. |

SEE ALSO

[Sharding](https://docs.mongodb.com/manual/sharding/)for more information about MongoDB’s sharding functionality.

### Instance Administration Commands

| Name | Description |
| :--- | :--- |
| [`clean`](https://docs.mongodb.com/manual/reference/command/clean/#dbcmd.clean) | Internal namespace administration command. |
| [`clone`](https://docs.mongodb.com/manual/reference/command/clone/#dbcmd.clone) | Copies a database from a remote host to the current host. |
| [`cloneCollection`](https://docs.mongodb.com/manual/reference/command/cloneCollection/#dbcmd.cloneCollection) | Copies a collection from a remote host to the current host. |
| [`cloneCollectionAsCapped`](https://docs.mongodb.com/manual/reference/command/cloneCollectionAsCapped/#dbcmd.cloneCollectionAsCapped) | Copies a non-capped collection as a new[capped collection](https://docs.mongodb.com/manual/reference/glossary/#term-capped-collection). |
| [`collMod`](https://docs.mongodb.com/manual/reference/command/collMod/#dbcmd.collMod) | Add options to a collection or modify a view definition. |
| [`compact`](https://docs.mongodb.com/manual/reference/command/compact/#dbcmd.compact) | Defragments a collection and rebuilds the indexes. |
| [`connPoolSync`](https://docs.mongodb.com/manual/reference/command/connPoolSync/#dbcmd.connPoolSync) | Internal command to flush connection pool. |
| [`connectionStatus`](https://docs.mongodb.com/manual/reference/command/connectionStatus/#dbcmd.connectionStatus) | Reports the authentication state for the current connection. |
| [`convertToCapped`](https://docs.mongodb.com/manual/reference/command/convertToCapped/#dbcmd.convertToCapped) | Converts a non-capped collection to a capped collection. |
| [`copydb`](https://docs.mongodb.com/manual/reference/command/copydb/#dbcmd.copydb) | Copies a database from a remote host to the current host. |
| [`create`](https://docs.mongodb.com/manual/reference/command/create/#dbcmd.create) | Creates a collection or a view. |
| [`createIndexes`](https://docs.mongodb.com/manual/reference/command/createIndexes/#dbcmd.createIndexes) | Builds one or more indexes for a collection. |
| [`currentOp`](https://docs.mongodb.com/manual/reference/command/currentOp/#dbcmd.currentOp) | Returns a document that contains information on in-progress operations for the database instance. |
| [`drop`](https://docs.mongodb.com/manual/reference/command/drop/#dbcmd.drop) | Removes the specified collection from the database. |
| [`dropDatabase`](https://docs.mongodb.com/manual/reference/command/dropDatabase/#dbcmd.dropDatabase) | Removes the current database. |
| [`dropIndexes`](https://docs.mongodb.com/manual/reference/command/dropIndexes/#dbcmd.dropIndexes) | Removes indexes from a collection. |
| [`filemd5`](https://docs.mongodb.com/manual/reference/command/filemd5/#dbcmd.filemd5) | Returns the[md5](https://docs.mongodb.com/manual/reference/glossary/#term-md5)hash for files stored using[GridFS](https://docs.mongodb.com/manual/reference/glossary/#term-gridfs). |
| [`fsync`](https://docs.mongodb.com/manual/reference/command/fsync/#dbcmd.fsync) | Flushes pending writes to the storage layer and locks the database to allow backups. |
| [`getParameter`](https://docs.mongodb.com/manual/reference/command/getParameter/#dbcmd.getParameter) | Retrieves configuration options. |
| [`killCursors`](https://docs.mongodb.com/manual/reference/command/killCursors/#dbcmd.killCursors) | Kills the specified cursors for a collection. |
| [`killOp`](https://docs.mongodb.com/manual/reference/command/killOp/#dbcmd.killOp) | Terminates an operation as specified by the operation ID. |
| [`listCollections`](https://docs.mongodb.com/manual/reference/command/listCollections/#dbcmd.listCollections) | Returns a list of collections in the current database. |
| [`listIndexes`](https://docs.mongodb.com/manual/reference/command/listIndexes/#dbcmd.listIndexes) | Lists all indexes for a collection. |
| [`logRotate`](https://docs.mongodb.com/manual/reference/command/logRotate/#dbcmd.logRotate) | Rotates the MongoDB logs to prevent a single file from taking too much space. |
| [`reIndex`](https://docs.mongodb.com/manual/reference/command/reIndex/#dbcmd.reIndex) | Rebuilds all indexes on a collection. |
| [`renameCollection`](https://docs.mongodb.com/manual/reference/command/renameCollection/#dbcmd.renameCollection) | Changes the name of an existing collection. |
| [`repairCursor`](https://docs.mongodb.com/manual/reference/command/repairCursor/#dbcmd.repairCursor) | Returns a cursor that iterates over all valid documents in a collection. |
| [`repairDatabase`](https://docs.mongodb.com/manual/reference/command/repairDatabase/#dbcmd.repairDatabase) | Rebuilds the database and indexes by discarding invalid or corrupt data. |
| [`setFeatureCompatibilityVersion`](https://docs.mongodb.com/manual/reference/command/setFeatureCompatibilityVersion/#dbcmd.setFeatureCompatibilityVersion) | Enables or disables MongoDB 3.4 features that persist data that are backwards-incompatible with MongoDB 3.2. |
| [`setParameter`](https://docs.mongodb.com/manual/reference/command/setParameter/#dbcmd.setParameter) | Modifies configuration options. |
| [`shutdown`](https://docs.mongodb.com/manual/reference/command/shutdown/#dbcmd.shutdown) | Shuts down the[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)or[`mongos`](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos)process. |
| [`touch`](https://docs.mongodb.com/manual/reference/command/touch/#dbcmd.touch) | Loads documents and indexes from data storage to memory. |

### Diagnostic Commands

| Name | Description |
| :--- | :--- |
| [`availableQueryOptions`](https://docs.mongodb.com/manual/reference/command/availableQueryOptions/#dbcmd.availableQueryOptions) | Internal command that reports on the capabilities of the current MongoDB instance. |
| [`buildInfo`](https://docs.mongodb.com/manual/reference/command/buildInfo/#dbcmd.buildInfo) | Displays statistics about the MongoDB build. |
| [`collStats`](https://docs.mongodb.com/manual/reference/command/collStats/#dbcmd.collStats) | Reports storage utilization statics for a specified collection. |
| [`connPoolStats`](https://docs.mongodb.com/manual/reference/command/connPoolStats/#dbcmd.connPoolStats) | Reports statistics on the outgoing connections from this MongoDB instance to other MongoDB instances in the deployment. |
| [`cursorInfo`](https://docs.mongodb.com/manual/reference/command/cursorInfo/#dbcmd.cursorInfo) | Removed in MongoDB 3.2. Replaced with[`metrics.cursor`](https://docs.mongodb.com/manual/reference/command/serverStatus/#serverstatus.metrics.cursor). |
| [`dataSize`](https://docs.mongodb.com/manual/reference/command/dataSize/#dbcmd.dataSize) | Returns the data size for a range of data. For internal use. |
| [`dbHash`](https://docs.mongodb.com/manual/reference/command/dbHash/#dbcmd.dbHash) | Returns hash value a database and its collections. |
| [`dbStats`](https://docs.mongodb.com/manual/reference/command/dbStats/#dbcmd.dbStats) | Reports storage utilization statistics for the specified database. |
| [`diagLogging`](https://docs.mongodb.com/manual/reference/command/diagLogging/#dbcmd.diagLogging) | Provides a diagnostic logging. For internal use. |
| [`driverOIDTest`](https://docs.mongodb.com/manual/reference/command/driverOIDTest/#dbcmd.driverOIDTest) | Internal command that converts an ObjectId to a string to support tests. |
| [`explain`](https://docs.mongodb.com/manual/reference/command/explain/#dbcmd.explain) | Returns information on the execution of various operations. |
| [`features`](https://docs.mongodb.com/manual/reference/command/features/#dbcmd.features) | Reports on features available in the current MongoDB instance. |
| [`getCmdLineOpts`](https://docs.mongodb.com/manual/reference/command/getCmdLineOpts/#dbcmd.getCmdLineOpts) | Returns a document with the run-time arguments to the MongoDB instance and their parsed options. |
| [`getLog`](https://docs.mongodb.com/manual/reference/command/getLog/#dbcmd.getLog) | Returns recent log messages. |
| [`hostInfo`](https://docs.mongodb.com/manual/reference/command/hostInfo/#dbcmd.hostInfo) | Returns data that reflects the underlying host system. |
| `isSelf` | Internal command to support testing. |
| [`listCommands`](https://docs.mongodb.com/manual/reference/command/listCommands/#dbcmd.listCommands) | Lists all database commands provided by the current[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)instance. |
| [`listDatabases`](https://docs.mongodb.com/manual/reference/command/listDatabases/#dbcmd.listDatabases) | Returns a document that lists all databases and returns basic database statistics. |
| [`netstat`](https://docs.mongodb.com/manual/reference/command/netstat/#dbcmd.netstat) | Internal command that reports on intra-deployment connectivity. Only available for[`mongos`](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos)instances. |
| [`ping`](https://docs.mongodb.com/manual/reference/command/ping/#dbcmd.ping) | Internal command that tests intra-deployment connectivity. |
| [`profile`](https://docs.mongodb.com/manual/reference/command/profile/#dbcmd.profile) | Interface for the[database profiler](https://docs.mongodb.com/manual/reference/database-profiler/#profiler). |
| [`serverStatus`](https://docs.mongodb.com/manual/reference/command/serverStatus/#dbcmd.serverStatus) | Returns a collection metrics on instance-wide resource utilization and status. |
| [`shardConnPoolStats`](https://docs.mongodb.com/manual/reference/command/shardConnPoolStats/#dbcmd.shardConnPoolStats) | Reports statistics on a[`mongos`](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos)’s connection pool for client operations against shards. |
| [`top`](https://docs.mongodb.com/manual/reference/command/top/#dbcmd.top) | Returns raw usage statistics for each database in the[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)instance. |
| [`validate`](https://docs.mongodb.com/manual/reference/command/validate/#dbcmd.validate) | Internal command that scans for a collection’s data and indexes for correctness. |
| [`whatsmyuri`](https://docs.mongodb.com/manual/reference/command/whatsmyuri/#dbcmd.whatsmyuri) | Internal command that returns information on the current client. |

## Auditing Commands

| Name | Description |
| :--- | :--- |
| [`logApplicationMessage`](https://docs.mongodb.com/manual/reference/command/logApplicationMessage/#dbcmd.logApplicationMessage) | Posts a custom message to the audit log. |



