# Production Notes

On this page

* [MongoDB Binaries](https://docs.mongodb.com/manual/administration/production-notes/#mongodb-binaries)
* [MongoDB`dbPath`](https://docs.mongodb.com/manual/administration/production-notes/#mongodb-dbpath)
* [Concurrency](https://docs.mongodb.com/manual/administration/production-notes/#concurrency)
* [Data Consistency](https://docs.mongodb.com/manual/administration/production-notes/#data-consistency)
* [Networking](https://docs.mongodb.com/manual/administration/production-notes/#networking)
* [Hardware Considerations](https://docs.mongodb.com/manual/administration/production-notes/#hardware-considerations)
* [Architecture](https://docs.mongodb.com/manual/administration/production-notes/#architecture)
* [Compression](https://docs.mongodb.com/manual/administration/production-notes/#compression)
* [Clock Synchronization](https://docs.mongodb.com/manual/administration/production-notes/#clock-synchronization)
* [Platform Specific Considerations](https://docs.mongodb.com/manual/administration/production-notes/#platform-specific-considerations)
* [Performance Monitoring](https://docs.mongodb.com/manual/administration/production-notes/#performance-monitoring)
* [Backups](https://docs.mongodb.com/manual/administration/production-notes/#backups)
* [Additional Resources](https://docs.mongodb.com/manual/administration/production-notes/#additional-resources)

This page details system configurations that affect MongoDB, especially when running in production.

NOTE

[MongoDB Atlas](https://www.mongodb.com/cloud/atlas?jmp=docs)is a cloud-hosted database-as-a-service.[MongoDB Cloud Manager](https://www.mongodb.com/cloud/cloud-manager/?jmp=docs), a hosted service, and[Ops Manager](https://www.mongodb.com/products/mongodb-enterprise-advanced?jmp=docs), an on-premise solution, provide monitoring, backup, and automation of MongoDB instances. For documentation, see[Atlas documentation](https://docs.atlas.mongodb.com/?jmp=docs), the[MongoDB Cloud Manager documentation](https://docs.cloudmanager.mongodb.com/)and[Ops Manager documentation](https://docs.opsmanager.mongodb.com/?jmp=docs)

## MongoDB Binaries

### Supported Platforms

For running**in production**, refer to the[Recommended Platforms](https://docs.mongodb.com/manual/administration/production-notes/#prod-notes-recommended-platforms)for operating system recommendations.

Changed in version 3.2:MongoDB can now use the[WiredTiger storage engine](https://docs.mongodb.com/manual/core/wiredtiger/#storage-wiredtiger)on all supported platforms.

#### x86\_64

| Platform | 3.4 Community | & | Enterprise | 3.2 Community | & | Enterprise | 3.0 Community | & | Enterprise |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
|  |  |  |  |  |  | Amazon Linux 2013.03 and later | ✓ | ✓ | ✓ |
|  |  |  |  |  |  | Debian 7 | ✓ | ✓ | ✓ |
|  |  |  |  |  |  | Debian 8 | ✓ | ✓ |  |
|  |  |  |  |  |  | RHEL/CentOS 6.2 and later | ✓ | ✓ | ✓ |
|  |  |  |  |  |  | RHEL/CentOS 7.0 and later | ✓ | ✓ | ✓ |
|  |  |  |  |  |  | SLES 11 | ✓ | ✓ | ✓ |
|  |  |  |  |  |  | SLES 12 | ✓ |  |  |
|  |  |  |  |  |  | Solaris 11 64-bit | Community only | Community only | Community only |
|  |  |  |  |  |  | Ubuntu 12.04 | ✓ | ✓ | ✓ |
|  |  |  |  |  |  | Ubuntu 14.04 | ✓ | ✓ | ✓ |
|  |  |  |  |  |  | Ubuntu 16.04 | ✓ | ✓ |  |
|  |  |  |  |  |  | Windows Server 2008R2 and later | ✓ | ✓ | ✓ |
|  |  |  |  |  |  | Windows Vista and later | ✓ | ✓ | ✓ |
|  |  |  |  |  |  | OS X 10.7 and later | ✓ | ✓ |  |

#### ARM64

| Platform | 3.4 Community | & | Enterprise |
| :--- | :--- | :--- | :--- |
|  |  | Ubuntu 16.04 | ✓ |

#### PPC64LE \(MongoDB Enterprise Edition\)

| Platform | 3.4 Enterprise |
| :--- | :--- |
| RHEL/CentOS 7.1 | ✓ |
| Ubuntu 16.04 | ✓ |

#### s390x \(MongoDB Enterprise Edition\)

| Platform | 3.4 Enterprise |
| :--- | :--- |
| RHEL/CentOS 7.2 | ✓ |
| SLES 11 | ✓ |
| SLES 12 | ✓ |
| Ubuntu 16.04 | ✓ |

IMPORTANT

MMAPv1 is not supported on big-endian architectures such as s390x. MongoDB returns an error if you set MMAPv1 as the storage engine on a big-endian system.

### Recommended Platforms

While MongoDB supports a variety of platforms, the following operating systems are recommended for production use:

* Amazon Linux
* Debian 7.1
* RHEL / CentOS 6.2+
* SLES 11+
* Ubuntu LTS 12.04
* Ubuntu LTS 14.04
* Windows Server 2012 
  &
   2012 R2

SEE ALSO

[Platform Specific Considerations](https://docs.mongodb.com/manual/administration/production-notes/#prod-notes-platform-considerations)

### Use the Latest Stable Packages

Be sure you have the latest stable release.

All MongoDB releases are available on the[Downloads](http://www.mongodb.org/downloads)page. The[Downloads](http://www.mongodb.org/downloads)page is a good place to verify the current stable release, even if you are installing via a package manager.

The following summarizes the supported architecture for the latest version of MongoDB products:

| Product | x86\_64/amd64 | s390x | POWER8 \(little endian\) | ARMv8-A |
| :--- | :--- | :--- | :--- | :--- |
| MongoDB 3.4 | ✓ | MongoDB Enterprise only | MongoDB Enterprise only | ✓ |
| [BI Connector](https://docs.mongodb.com/bi-connector/v2.0/) | ✓ | ✓ | ✓ |  |
| [Compass](https://docs.mongodb.com/compass/current/) | ✓ |  |  |  |
| [Spark Connector](https://docs.mongodb.com/spark-connector/v2.0/) | ✓ |  |  |  |
| [Ops Manager](https://docs.opsmanager.mongodb.com/current/) | ✓ |  |  |  |
| Automation Agent | ✓ |  | ✓ |  |
| Monitoring Agent | ✓ |  | ✓ |  |
| Backup Agent | ✓ |  | ✓ |  |

## MongoDB`dbPath`

The files in the[`dbPath`](https://docs.mongodb.com/manual/reference/configuration-options/#storage.dbPath)directory must correspond to the configured[storage engine](https://docs.mongodb.com/manual/reference/glossary/#term-storage-engine).[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)will not start if[`dbPath`](https://docs.mongodb.com/manual/reference/configuration-options/#storage.dbPath)contains data files created by a storage engine other than the one specified by[`--storageEngine`](https://docs.mongodb.com/manual/reference/program/mongod/#cmdoption-storageengine).

Changed in version 3.2:As of MongoDB 3.2, MongoDB uses the[WiredTiger](https://docs.mongodb.com/manual/core/wiredtiger/#storage-wiredtiger)storage engine by default.

Changed in version 3.0:MongoDB includes support for two storage engines:[MMAPv1](https://docs.mongodb.com/manual/core/mmapv1/#storage-mmapv1), the storage engine available in previous versions of MongoDB, and[WiredTiger](https://docs.mongodb.com/manual/core/wiredtiger/#storage-wiredtiger).

[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)must possess read and write permissions for the specified[`dbPath`](https://docs.mongodb.com/manual/reference/configuration-options/#storage.dbPath).

## Concurrency

### MMAPv1

Changed in version 3.0:Beginning with MongoDB 3.0,[MMAPv1](https://docs.mongodb.com/manual/core/mmapv1/#storage-mmapv1)provides_collection-level locking_: All collections have a unique readers-writer lock that allows multiple clients to modify documents in different collections at the same time.

For MongoDB versions 2.2 through 2.6 series, each database has a readers-writer lock that allows concurrent read access to a database, but gives exclusive access to a single write operation per database. See the[Concurrency](https://docs.mongodb.com/manual/faq/concurrency/)page for more information. In earlier versions of MongoDB, all write operations contended for a single readers-writer lock for the entire[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)instance.

### WiredTiger

[WiredTiger](https://docs.mongodb.com/manual/core/wiredtiger/#storage-wiredtiger)supports concurrent access by readers and writers to the documents in a collection. Clients can read documents while write operations are in progress, and multiple threads can modify different documents in a collection at the same time.

SEE ALSO

[Allocate Sufficient RAM and CPU](https://docs.mongodb.com/manual/administration/production-notes/#prod-notes-ram)provides information about how WiredTiger takes advantage of multiple CPU cores and how to improve operation throughput.

## Data Consistency

### Journaling

MongoDB uses\_write ahead logging\_to an on-disk[journal](https://docs.mongodb.com/manual/reference/glossary/#term-journal). Journaling guarantees that MongoDB can quickly recover[write operations](https://docs.mongodb.com/manual/crud/)that were written to the journal but not written to data files in cases where[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)terminated due to a crash or other serious failure.

Leave journaling enabled in order to ensure that[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)will be able to recover its data files and keep the data files in a valid state following a crash. See[Journaling](https://docs.mongodb.com/manual/core/journaling/)for more information.

### Read Concern

New in version 3.2.

If using\[`"majority"`\]\([https://docs.mongodb.com/manual/reference/read-concern/\#readconcern."majority"\)or\[\`"linearizable"\`\]\(https://docs.mongodb.com/manual/reference/read-concern/\#readconcern."linearizable"\)read](https://docs.mongodb.com/manual/reference/read-concern/#readconcern."majority"%29or[`"linearizable"`]%28https://docs.mongodb.com/manual/reference/read-concern/#readconcern."linearizable"%29read) concern for read operations, use\[`{w:"majority"}`\]\([https://docs.mongodb.com/manual/reference/write-concern/\#writeconcern."majority"\)write](https://docs.mongodb.com/manual/reference/write-concern/#writeconcern."majority"%29write) concern for write operations on the primary to ensure that a single thread can read its own writes.

To use[read concern](https://docs.mongodb.com/manual/reference/glossary/#term-read-concern)level of\[`"majority"`\]\([https://docs.mongodb.com/manual/reference/read-concern/\#readconcern."majority](https://docs.mongodb.com/manual/reference/read-concern/#readconcern."majority)"\),

* you must start the
  [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)
  instances with the
  [`--enableMajorityReadConcern`](https://docs.mongodb.com/manual/reference/program/mongod/#cmdoption-enablemajorityreadconcern)
  command line option \(or the
  [`replication.enableMajorityReadConcern`](https://docs.mongodb.com/manual/reference/configuration-options/#replication.enableMajorityReadConcern)
  set to
  `true`
  if using a configuration file\).
* replica sets must use
  [WiredTiger storage engine](https://docs.mongodb.com/manual/core/wiredtiger/#storage-wiredtiger)
  and election
  [`protocolversion1`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.protocolVersion)
  .

### Write Concern

[Write concern](https://docs.mongodb.com/manual/reference/write-concern/)describes the level of acknowledgement requested from MongoDB for write operations. The level of the write concerns affects how quickly the write operation returns. When write operations have a\_weak\_write concern, they return quickly. With\_stronger\_write concerns, clients must wait after sending a write operation until MongoDB confirms the write operation at the requested write concern level. With insufficient write concerns, write operations may appear to a client to have succeeded, but may not persist in some cases of server failure.

See the[Write Concern](https://docs.mongodb.com/manual/reference/write-concern/)document for more information about choosing an appropriate write concern level for your deployment.

## Networking

### Use Trusted Networking Environments

Always run MongoDB in a_trusted environment_, with network rules that prevent access from\_all\_unknown machines, systems, and networks. As with any sensitive system that is dependent on network access, your MongoDB deployment should only be accessible to specific systems that require access, such as application servers, monitoring services, and other MongoDB components.

IMPORTANT

By default,[authorization](https://docs.mongodb.com/manual/core/authorization/)is not enabled, and[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)assumes a trusted environment. Enable[`authorization`](https://docs.mongodb.com/manual/reference/configuration-options/#security.authorization)mode as needed. For more information on authentication mechanisms supported in MongoDB as well as authorization in MongoDB, see[Authentication](https://docs.mongodb.com/manual/core/authentication/)and[Role-Based Access Control](https://docs.mongodb.com/manual/core/authorization/).

For additional information and considerations on security, refer to the documents in the[Security Section](https://docs.mongodb.com/manual/security/), specifically:

* [Security Checklist](https://docs.mongodb.com/manual/administration/security-checklist/)
* [MongoDB Configuration Hardening](https://docs.mongodb.com/manual/core/security-mongodb-configuration/)
* [Hardening Network Infrastructure](https://docs.mongodb.com/manual/core/security-network/)

For Windows users, consider the[Windows Server Technet Article on TCP Configuration](http://technet.microsoft.com/en-us/library/dd349797.aspx)when deploying MongoDB on Windows.

### Disable HTTP Interface

MongoDB provides an HTTP interface to check the status of the server and, optionally, run queries. The HTTP interface is disabled by default. Do not enable the HTTP interface in production environments.

Deprecated since version 3.2:HTTP interface for MongoDB

See[HTTP Status Interface](https://docs.mongodb.com/manual/core/security-mongodb-configuration/#http-interface-security).

### Manage Connection Pool Sizes

Avoid overloading the connection resources of a[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)or[`mongos`](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos)instance by adjusting the connection pool size to suit your use case. Start at 110-115% of the typical number of current database requests, and modify the connection pool size as needed. Refer to the[Connection Pool Options](https://docs.mongodb.com/manual/reference/connection-string/#connection-pool-options)for adjusting the connection pool size.

The[`connPoolStats`](https://docs.mongodb.com/manual/reference/command/connPoolStats/#dbcmd.connPoolStats)command returns information regarding the number of open connections to the current database for[`mongos`](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos)and[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)instances in sharded clusters.

See also[Allocate Sufficient RAM and CPU](https://docs.mongodb.com/manual/administration/production-notes/#prod-notes-ram).

## Hardware Considerations

MongoDB is designed specifically with commodity hardware in mind and has few hardware requirements or limitations. MongoDB’s core components run on little-endian hardware, primarily x86/x86\_64 processors. Client libraries \(i.e. drivers\) can run on big or little endian systems.

### Allocate Sufficient RAM and CPU

#### MMAPv1

Due to its concurrency model, the[MMAPv1](https://docs.mongodb.com/manual/core/mmapv1/#storage-mmapv1)storage engine does not require many CPU cores. As such, increasing the number of cores can improve performance but does not provide significant return.

At a minimum, ensure that your[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)or[`mongos`](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos)has access to two real cores or one physical CPU.

Increasing the amount of RAM accessible to MongoDB may help reduce the frequency of page faults.

#### WiredTiger

The[WiredTiger](https://docs.mongodb.com/manual/core/wiredtiger/#storage-wiredtiger)storage engine is multithreaded and can take advantage of additional CPU cores. Specifically, the total number of active threads \(i.e. concurrent operations\) relative to the number of available CPUs can impact performance:

* Throughput
  _increases_
  as the number of concurrent active operations increases up to the number of CPUs.
* Throughput
  _decreases_
  as the number of concurrent active operations exceeds the number of CPUs by some threshold amount.

The threshold depends on your application. You can determine the optimum number of concurrent active operations for your application by experimenting and measuring throughput. The output from[`mongostat`](https://docs.mongodb.com/manual/reference/program/mongostat/#bin.mongostat)provides statistics on the number of active reads/writes in the \(`ar|aw`\) column.

With WiredTiger, MongoDB utilizes both the WiredTiger internal cache and the filesystem cache.

Starting in 3.4, the WiredTiger internal cache, by default, will use the larger of either:

* 50% of RAM minus 1 GB, or
* 256 MB.

By default, WiredTiger uses Snappy block compression for all collections and prefix compression for all indexes. Compression defaults are configurable at a global level and can also be set on a per-collection and per-index basis during collection and index creation.

Different representations are used for data in the WiredTiger internal cache versus the on-disk format:

* Data in the filesystem cache is the same as the on-disk format, including benefits of any compression for data files. The filesystem cache is used by the operating system to reduce disk I/O.
* Indexes loaded in the WiredTiger internal cache have a different data representation to the on-disk format, but can still take advantage of index prefix compression to reduce RAM usage. Index prefix compression deduplicates common prefixes from indexed fields.
* Collection data in the WiredTiger internal cache is uncompressed and uses a different representation from the on-disk format. Block compression can provide significant on-disk storage savings, but data must be uncompressed to be manipulated by the server.

Via the filesystem cache, MongoDB automatically uses all free memory that is not used by the WiredTiger cache or by other processes.

To adjust the size of the WiredTiger internal cache, see[`storage.wiredTiger.engineConfig.cacheSizeGB`](https://docs.mongodb.com/manual/reference/configuration-options/#storage.wiredTiger.engineConfig.cacheSizeGB)and[`--wiredTigerCacheSizeGB`](https://docs.mongodb.com/manual/reference/program/mongod/#cmdoption-wiredtigercachesizegb). Avoid increasing the WiredTiger internal cache size above its default value.

NOTE

The[`storage.wiredTiger.engineConfig.cacheSizeGB`](https://docs.mongodb.com/manual/reference/configuration-options/#storage.wiredTiger.engineConfig.cacheSizeGB)limits the size of the WiredTiger internal cache. The operating system will use the available free memory for filesystem cache, which allows the compressed MongoDB data files to stay in memory. In addition, the operating system will use any free RAM to buffer file system blocks and file system cache.

To accommodate the additional consumers of RAM, you may have to decrease WiredTiger internal cache size.

The default WiredTiger internal cache size value assumes that there is a single[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)instance per machine. If a single machine contains multiple MongoDB instances, then you should decrease the setting to accommodate the other[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)instances.

If you run[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)in a container \(e.g.`lxc`,`cgroups`, Docker, etc.\) that does\_not\_have access to all of the RAM available in a system, you must set[`storage.wiredTiger.engineConfig.cacheSizeGB`](https://docs.mongodb.com/manual/reference/configuration-options/#storage.wiredTiger.engineConfig.cacheSizeGB)to a value less than the amount of RAM available in the container. The exact amount depends on the other processes running in the container.

To view statistics on the cache and eviction rate, see the[`wiredTiger.cache`](https://docs.mongodb.com/manual/reference/command/serverStatus/#serverstatus.wiredTiger.cache)field returned from the[`serverStatus`](https://docs.mongodb.com/manual/reference/command/serverStatus/#dbcmd.serverStatus)command.

#### Compression and Encryption

When using encryption, CPUs equipped with AES-NI instruction-set extensions show significant performance advantages. If you are using MongoDB Enterprise with the[Encrypted Storage Engine](https://docs.mongodb.com/manual/core/security-encryption-at-rest/#encrypted-storage-engine), choose a CPU that supports AES-NI for better performance.

SEE ALSO

[Concurrency](https://docs.mongodb.com/manual/administration/production-notes/#prod-notes-concurrency)

### Use Solid State Disks \(SSDs\)

MongoDB has good results and a good price-performance ratio with SATA SSD \(Solid State Disk\).

Use SSD if available and economical. Spinning disks can be performant, but SSDs’ capacity for random I/O operations works well with the update model of MMAPv1.

Commodity \(SATA\) spinning drives are often a good option, as the random I/O performance increase with more expensive spinning drives is not that dramatic \(only on the order of 2x\). Using SSDs or increasing RAM may be more effective in increasing I/O throughput.

### MongoDB and NUMA Hardware

Running MongoDB on a system with Non-Uniform Access Memory \(NUMA\) can cause a number of operational problems, including slow performance for periods of time and high system process usage.

When running MongoDB servers and clients on NUMA hardware, you should configure a memory interleave policy so that the host behaves in a non-NUMA fashion. MongoDB checks NUMA settings on start up when deployed on Linux \(since version 2.0\) and Windows \(since version 2.6\) machines. If the NUMA configuration may degrade performance, MongoDB prints a warning.

SEE ALSO

* [The MySQL “swap insanity” problem and the effects of NUMA](http://jcole.us/blog/archives/2010/09/28/mysql-swap-insanity-and-the-numa-architecture/)
  post, which describes the effects of NUMA on databases. The post introduces NUMA and its goals, and illustrates how these goals are not compatible with production databases. Although the blog post addresses the impact of NUMA for MySQL, the issues for MongoDB are similar.
* [NUMA: An Overview](https://queue.acm.org/detail.cfm?id=2513149)
  .

#### Configuring NUMA on Windows

On Windows, memory interleaving must be enabled through the machine’s BIOS. Consult your system documentation for details.

#### Configuring NUMA on Linux

When running MongoDB on Linux, you should disable\_zone reclaim\_in the`sysctl`settings using one of the following commands:

```
echo
0
|
 sudo tee /proc/sys/vm/zone_reclaim_mode
```

```
sudo sysctl -w vm.zone_reclaim_mode
=
0
```

Then, you should use`numactl`to start your[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)instances, including the[config servers](https://docs.mongodb.com/manual/core/sharded-cluster-config-servers/),[`mongos`](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos)instances, and any clients. If you do not have the`numactl`command, refer to the documentation for your operating system to install the`numactl`package.

The following operation demonstrates how to start a MongoDB instance using`numactl`:

```
numactl --interleave
=
all 
<
path
>
<
options
>
```

The`<path>`is the path to the program you are starting and the`<options>`are any optional arguments to pass to the program.

To fully disable NUMA behavior, you must perform both operations. For more information, see the[Documentation for /proc/sys/vm/\*](http://www.kernel.org/doc/Documentation/sysctl/vm.txt).

### Disk and Storage Systems

#### Swap

Assign swap space for your systems. Allocating swap space can avoid issues with memory contention and can prevent the OOM Killer on Linux systems from killing[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod).

For the MMAPv1 storage engine, the method[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)uses to map files to memory ensures that the operating system will never store MongoDB data in swap space. On Windows systems, using MMAPv1 requires extra swap space due to commitment limits. For details, see[MongoDB on Windows](https://docs.mongodb.com/manual/administration/production-notes/#production-windows-pagefile).

For the WiredTiger storage engine, given sufficient memory pressure, WiredTiger may store data in swap space.

#### RAID

For optimal performance in terms of the storage layer, use disks backed by RAID-10. RAID-5 and RAID-6 do not typically provide sufficient performance to support a MongoDB deployment.

Avoid RAID-0 with MongoDB deployments. While RAID-0 provides good write performance, it also provides limited availability and can lead to reduced performance on read operations, particularly when using Amazon’s EBS volumes.

#### Remote Filesystems

With the MMAPv1 storage engine, the Network File System protocol \(NFS\) is not recommended as you may see performance problems when both the data files and the journal files are hosted on NFS. You may experience better performance if you place the journal on local or`iscsi`volumes.

With the WiredTiger storage engine, WiredTiger objects may be stored on remote file systems if the remote file system conforms to ISO/IEC 9945-1:1996 \(POSIX.1\). Because remote file systems are often slower than local file systems, using a remote file system for storage may degrade performance.

If you decide to use NFS, add the following NFS options to your`/etc/fstab`file:`bg`,`nolock`, and`noatime`.

#### Separate Components onto Different Storage Devices

For improved performance, consider separating your database’s data, journal, and logs onto different storage devices, based on your application’s access and write pattern. Mount the components as separate filesystems and use symbolic links to map each component’s path to the device storing it.

For the WiredTiger storage engine, you can also store the indexes on a different storage device. See[`storage.wiredTiger.engineConfig.directoryForIndexes`](https://docs.mongodb.com/manual/reference/configuration-options/#storage.wiredTiger.engineConfig.directoryForIndexes).

NOTE

Using different storage devices will affect your ability to create snapshot-style backups of your data, since the files will be on different devices and volumes.

#### Scheduling

##### Scheduling for Virtual or Cloud Hosted Devices

For local block devices attached to a virtual machine instance via the hypervisor or hosted by a cloud hosting provider, the guest operating system should use a\_noop\_scheduler for best performance. The\_noop\_scheduler allows the operating system to defer I/O scheduling to the underlying hypervisor.

##### Scheduling for Physical Servers

For physical servers, the operating system should use a\_deadline\_scheduler. The\_deadline\_scheduler caps maximum latency per request and maintains a good disk throughput that is best for disk-intensive database applications.

## Architecture

### Replica Sets

See the[Replica Set Architectures](https://docs.mongodb.com/manual/core/replica-set-architectures/)document for an overview of architectural considerations for replica set deployments.

### Sharded Clusters

See[Sharded Cluster Production Architecture](https://docs.mongodb.com/manual/core/sharded-cluster-components/)for an overview of recommended sharded cluster architectures for production deployments.

SEE ALSO

[Development Checklist](https://docs.mongodb.com/manual/administration/production-checklist-development/)

## Compression

WiredTiger can compress collection data using either[snappy](https://docs.mongodb.com/manual/reference/glossary/#term-snappy)or[zlib](https://docs.mongodb.com/manual/reference/glossary/#term-zlib)compression library.[snappy](https://docs.mongodb.com/manual/reference/glossary/#term-snappy)provides a lower compression rate but has little performance cost, whereas`zlib`provides better compression rate but has a higher performance cost.

By default, WiredTiger uses[snappy](https://docs.mongodb.com/manual/reference/glossary/#term-snappy)compression library. To change the compression setting, see[`storage.wiredTiger.collectionConfig.blockCompressor`](https://docs.mongodb.com/manual/reference/configuration-options/#storage.wiredTiger.collectionConfig.blockCompressor).

WiredTiger uses[prefix compression](https://docs.mongodb.com/manual/reference/glossary/#term-prefix-compression)on all indexes by default.

## Clock Synchronization

Use[NTP](http://www.ntp.org/)to synchronize the clocks on all components of your MongoDB deployment.[Replica sets](https://docs.mongodb.com/manual/reference/glossary/#term-replica-set)and[sharded clusters](https://docs.mongodb.com/manual/reference/glossary/#term-sharded-cluster)running MongoDB 3.4.5 or earlier with the[WiredTiger](https://docs.mongodb.com/manual/core/wiredtiger/)storage engine may experience[checkpoint](https://docs.mongodb.com/manual/core/wiredtiger/#storage-wiredtiger-checkpoints)hangs on systems with unreliable clocks.

## Platform Specific Considerations

NOTE

MongoDB uses the[GNU C Library](http://www.gnu.org/software/libc/)\(glibc\) if available on a system. MongoDB requires version at least`glibc-2.12-1.2.el6`to avoid a known bug with earlier versions. For best results use at least version 2.13.

### MongoDB on Linux

#### Kernel and File Systems

When running MongoDB in production on Linux, you should use Linux kernel version 2.6.36 or later, with either the XFS or EXT4 filesystem. If possible, use XFS as it generally performs better with MongoDB.

With the[WiredTiger storage engine](https://docs.mongodb.com/manual/core/wiredtiger/#storage-wiredtiger), use of XFS is**strongly recommended**to avoid performance issues that may occur when using EXT4 with WiredTiger.

With the[MMAPv1 storage engine](https://docs.mongodb.com/manual/core/mmapv1/#storage-mmapv1), MongoDB preallocates its database files before using them and often creates large files. As such, you should use the XFS or EXT4 file systems. If possible, use XFS as it generally performs better with MongoDB.

* In general, if you use the XFS file system, use at least version
  `2.6.25`
  of the Linux Kernel.
* If you use the EXT4 file system, use at least version
  `2.6.28`
  of the Linux Kernel.
* On Red Hat Enterprise Linux and CentOS, use at least version
  `2.6.18-194`
  of the Linux kernel.

#### `fsync()`on Directories

IMPORTANT

MongoDB requires a filesystem that supports`fsync()`_on directories_. For example, HGFS and Virtual Box’s shared folders do\_not\_support this operation.

#### Recommended Configuration

For**all**MongoDB deployments:

* Use the Network Time Protocol \(NTP\) to synchronize time among your hosts. This is especially important in sharded clusters.

For the**WiredTiger and MMAPv1**storage engines, consider the following recommendations:

* Turn off`atime`for the storage volume containing the[database files](https://docs.mongodb.com/manual/reference/glossary/#term-dbpath).

* Set the file descriptor limit,`-n`, and the user process limit \(ulimit\),`-u`, above 20,000, according to the suggestions in the[ulimit](https://docs.mongodb.com/manual/reference/ulimit/)reference. A low ulimit will affect MongoDB when under heavy use and can produce errors and lead to failed connections to MongoDB processes and loss of service.

* Disable Transparent Huge Pages. MongoDB performs better with normal \(4096 bytes\) virtual memory pages. See[Transparent Huge Pages Settings](https://docs.mongodb.com/manual/tutorial/transparent-huge-pages/).

* Disable NUMA in your BIOS. If that is not possible, see[MongoDB on NUMA Hardware](https://docs.mongodb.com/manual/administration/production-notes/#production-numa).

* Problems have been reported when using MongoDB with SELinux enabled. To avoid issues, disable SELinux when possible.

  If you are using SELinux on Red Hat, you must configure SELinux to be able to run MongoDB. See:[Configure SELinux for MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/#install-rhel-configure-selinux)and[Configure SELinux for MongoDB Enterprise](https://docs.mongodb.com/manual/tutorial/install-mongodb-enterprise-on-red-hat/#install-enterprise-rhel-configure-selinux)for the required configuration.

  NOTE

  If you are using SELinux, any MongoDB operation that requires[server-side JavaScript](https://docs.mongodb.com/manual/core/server-side-javascript/)will result in segfault errors.[Disable Server-Side Execution of JavaScript](https://docs.mongodb.com/manual/core/server-side-javascript/#disable-server-side-js)describes how to disable execution of server-side JavaScript.

For the**WiredTiger**storage engine:

* Set the readahead setting to 0 regardless of storage media type \(spinning, SSD, etc.\).

  Setting a higher readahead benefits sequential I/O operations. However, since MongoDB disk access patterns are generally random, setting a higher readahead provides limited benefit or performance degradation. As such, for most workloads, a readahead of 0 provides optimal MongoDB performance.

  In general, set the readahead setting to 0 unless testing shows a measurable, repeatable, and reliable benefit in a higher readahead value.[MongoDB Professional Support](https://www.mongodb.com/products/enterprise-grade-support)can provide advice and guidance on non-zero readahead configurations.

For the**MMAPv1**storage engine:

* Ensure that readahead settings for the block devices that store the database files are appropriate. For random access use patterns, set low readahead values. A readahead of 32 \(16 kB\) often works well.

  For a standard block device, you can run`sudoblockdev--report`to get the readahead settings and`sudoblockdev--setra<value><device>`to change the readahead settings. Refer to your specific operating system manual for more information.

#### MongoDB and TLS/SSL Libraries

On Linux platforms, you may observe one of the following statements in the MongoDB log:

```
<
path to SSL libs
>
/libssl.so.
<
version
>
: no version information available (required by /usr/bin/mongod)

<
path to SSL libs
>
/libcrypto.so.
<
version
>
: no version information available (required by /usr/bin/mongod)
```

These warnings indicate that the system’s TLS/SSL libraries are different from the TLS/SSL libraries that the[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)was compiled against. Typically these messages do not require intervention; however, you can use the following operations to determine the symbol versions that[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)expects:

```
objdump -T 
<
path to mongod
>
/mongod 
|
 grep 
" SSL_"

objdump -T 
<
path to mongod
>
/mongod 
|
 grep 
" CRYPTO_"
```

These operations will return output that resembles one the of the following lines:

```
0000000000000000      DF *UND*       0000000000000000  libssl.so.10 SSL_write
0000000000000000      DF *UND*       0000000000000000  OPENSSL_1.0.0 SSL_write
```

The last two strings in this output are the symbol version and symbol name. Compare these values with the values returned by the following operations to detect symbol version mismatches:

```
objdump -T 
<
path to TLS/SSL libs
>
/libssl.so.1*
objdump -T 
<
path to TLS/SSL libs
>
/libcrypto.so.1*
```

This procedure is neither exact nor exhaustive: many symbols used by[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)from the`libcrypto`library do not begin with`CRYPTO_`.

### MongoDB on Windows

#### MongoDB 3.0 Using WiredTiger

For MongoDB instances using the WiredTiger storage engine, performance on Windows is comparable to performance on Linux.

#### MongoDB Using MMAPv1

##### Install Hotfix for MongoDB 2.6.6 and Later

Microsoft has released a hotfix for Windows 7 and Windows Server 2008 R2,[KB2731284](http://support.microsoft.com/kb/2731284), that repairs a bug in these operating systems’ use of memory-mapped files that adversely affects the performance of MongoDB using the MMAPv1 storage engine.

Install this hotfix to obtain significant performance improvements on MongoDB 2.6.6 and later releases in the 2.6 series, which use MMAPv1 exclusively, and on 3.0 and later when using MMAPv1 as the storage engine.

##### Configure Windows Page File For MMAPv1

Configure the page file such that the minimum and maximum page file size are equal and at least 32 GB. Use a multiple of this size if, during peak usage, you expect concurrent writes to many databases or collections. However, the page file size does not need to exceed the maximum size of the database.

A large page file is needed as Windows requires enough space to accommodate all regions of memory mapped files made writable during peak usage, regardless of whether writes actually occur.

The page file is not used for database storage and will not receive writes during normal MongoDB operation. As such, the page file will not affect performance, but it must exist and be large enough to accommodate Windows’ commitment rules during peak database use.

NOTE

Dynamic page file sizing is too slow to accommodate the rapidly fluctuating commit charge of an active MongoDB deployment. This can result in transient overcommitment situations that may lead to abrupt server shutdown with a VirtualProtect error 1455.

### MongoDB on Virtual Environments

This section describes considerations when running MongoDB in some of the more common virtual environments.

For all platforms, consider[Scheduling](https://docs.mongodb.com/manual/administration/production-notes/#virtualized-disks-scheduling).

#### EC2

MongoDB is compatible with EC2.[MongoDB Cloud Manager](https://www.mongodb.com/cloud/cloud-manager/?jmp=docs)provides integration with Amazon Web Services \(AWS\) and lets you deploy new EC2 instances directly from MongoDB Cloud Manager. See[Configure AWS Integration](https://docs.cloudmanager.mongodb.com/tutorial/configure-aws-settings/)for more details.

When available, enable AWS’s[Enhanced Networking](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html#enabling_enhanced_networking)for your instance. Not all instance types support Enhanced Networking. Refer to the[AWS documentation](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html#enabling_enhanced_networking)for more information.

#### Azure

Use[Premium Storage](https://azure.microsoft.com/en-us/documentation/articles/storage-premium-storage/). Microsoft Azure offers two general types of storage: Standard storage, and Premium storage. MongoDB on Azure has better performance when using Premium storage than it does with Standard storage.

For all[MMAPv1](https://docs.mongodb.com/manual/core/mmapv1/#storage-mmapv1)MongoDB deployments using Azure, you**must**mount the volume that hosts the[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)instance’s[`dbPath`](https://docs.mongodb.com/manual/reference/configuration-options/#storage.dbPath)with the_Host Cache Preference_`READ/WRITE`. This applies to all Azure deployments running MMAPv1, using any guest operating system.

If your volumes have inappropriate cache settings, MongoDB may eventually shut down with the following error:

```
[DataFileSync] FlushViewOfFile for 
<
data file
>
 failed with error 1 ...
[DataFileSync] Fatal Assertion 16387
```

These shut downs do not produce data loss when[`storage.journal.enabled`](https://docs.mongodb.com/manual/reference/configuration-options/#storage.journal.enabled)is set to`true`. You can safely restart[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)at any time following this event.

The performance characteristics of MongoDB may change with`READ/WRITE`caching enabled.

The TCP idle timeout on the Azure load balancer is 240 seconds by default, which can cause it to silently drop connections if the TCP keepalive on your Azure systems is greater than this value. You should set`tcp_keepalive_time`to 120 to ameliorate this problem.

NOTE

You will need to restart[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)and[`mongos`](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos)processes for new system-wide keepalive settings to take effect.

**On Linux systems**:

* To view the keep alive setting, you can use one of the following commands:

  ```
  sysctl net.ipv4.tcp_keepalive_time
  ```

  Or:

  ```
  cat /proc/sys/net/ipv4/tcp_keepalive_time
  ```

  The value is measured in seconds.

* To change the`tcp_keepalive_time`value, you can use one of the following command:

  ```
  sudo sysctl -w net.ipv4.tcp_keepalive_time
  =
  <
  value
  >
  ```

  Or:

  ```
  echo
  <
  value
  >
  |
   sudo tee /proc/sys/net/ipv4/tcp_keepalive_time
  ```

  These operations do not persist across system reboots. To persist the setting, add the following line to`/etc/sysctl.conf`:

  ```
  net.ipv4.tcp_keepalive_time 
  =
  <
  value
  >
  ```

  On Linux,[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)and[`mongos`](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos)processes limit the keepalive to a maximum of 300 seconds \(5 minutes\) on their own sockets by overriding keepalive values greater than 5 minutes.

**For Windows systems**:

* To view the keep alive setting, issue the following command:

  ```
  reg
  query
  HKLM
  \
  SYSTEM
  \
  CurrentControlSet
  \
  Services
  \
  Tcpip
  \
  Parameters
  /
  v
  KeepAliveTime
  ```

  The registry value is not present by default. The system default, used if the value is absent, is 7200000\_milliseconds\_or`0x6ddd00`in hexadecimal.

* To change the`KeepAliveTime`value, use the following command in an AdministratorCommand Prompt, where`<value>`is expressed in hexadecimal \(e.g. 120000 is`0x1d4c0`\):

  ```
  reg
  add
  HKLM
  \
  SYSTEM
  \
  CurrentControlSet
  \
  Services
  \
  Tcpip
  \
  Parameters
  \
  /
  t
  REG_DWORD
  /
  v
  KeepAliveTime
  /
  d
  <
  value
  >
  ```

  Windows users should consider the[Windows Server Technet Article on KeepAliveTime](https://technet.microsoft.com/en-us/library/cc957549.aspx)for more information on setting keep alive for MongoDB deployments on Windows systems.

#### VMware

MongoDB is compatible with VMware.

VMware supports_memory overcommitment_, where you can assign more memory to your virtual machines than the physical machine has available. When memory is overcommitted, the hypervisor reallocates memory between the virtual machines. VMware’s balloon driver \(`vmmemctl`\) reclaims the pages that are considered least valuable. The balloon driver resides inside the guest operating system. When the balloon driver expands, it may induce the guest operating system to reclaim memory from guest applications, which can interfere with MongoDB’s memory management and affect MongoDB’s performance.

You can disable the balloon driver and VMware’s memory overcommitment feature to mitigate these problems. However, disabling the balloon driver can cause the hypervisor to use its swap, as there is no other available mechanism to perform the memory reclamation. Accessing data in swap is much slower than accessing data in memory, which can in turn affect performance. Instead of disabling the balloon driver and memory overcommitment features, map and reserve the full amount of memory for the virtual machine running MongoDB. This ensures that the balloon will not be inflated in the local operating system if there is memory pressure in the hypervisor due to an overcommitted configuration.

When using MongoDB with VMware, ensure that the CPU reservation does not exceed more than 2 virtual CPUs per physical core.

Ensure that virtual machines stay on a specific ESX/ESXi host by setting VMware’s[affinity rules](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=1005508&sliceId=1&docTypeID=DT_KB_1_1&dialogID=549881455&stateId=0 0 549889513). If you must manually migrate a virtual machine to another host and the[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)instance on the virtual machine is the[primary](https://docs.mongodb.com/manual/reference/glossary/#term-primary), you must first[`stepdown`](https://docs.mongodb.com/manual/reference/method/rs.stepDown/#rs.stepDown)the primary and then[`shutdowntheinstance`](https://docs.mongodb.com/manual/reference/method/db.shutdownServer/#db.shutdownServer).

Follow the[networking best practices for vMotion](https://docs.vmware.com/en/VMware-vSphere/6.0/com.vmware.vsphere.vcenterhost.doc/GUID-7DAD15D4-7F41-4913-9F16-567289E22977.html)and the[VMKernel](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2054994). Failure to follow the best practices can result in performance problems and affect[replica set](https://docs.mongodb.com/manual/core/replica-set-high-availability/)and[sharded cluster](https://docs.mongodb.com/manual/tutorial/troubleshoot-sharded-clusters/)high availability mechanisms.

It is possible to clone a virtual machine running MongoDB. You might use this function to spin up a new virtual host to add as a member of a replica set. If you clone a VM with journaling enabled, the clone snapshot will be valid. If not using journaling, first stop[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod), then clone the VM, and finally, restart[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod).

#### KVM

MongoDB is compatible with KVM.

KVM supports_memory overcommitment_, where you can assign more memory to your virtual machines than the physical machine has available. When memory is overcommitted, the hypervisor reallocates memory between the virtual machines. KVM’s balloon driver reclaims the pages that are considered least valuable. The balloon driver resides inside the guest operating system. When the balloon driver expands, it may induce the guest operating system to reclaim memory from guest applications, which can interfere with MongoDB’s memory management and affect MongoDB’s performance.

You can disable the balloon driver and KVM’s memory overcommitment feature to mitigate these problems. However, disabling the balloon driver can cause the hypervisor to use its swap, as there is no other available mechanism to perform the memory reclamation. Accessing data in swap is much slower than accessing data in memory, which can in turn affect performance. Instead of disabling the balloon driver and memory overcommitment features, map and reserve the full amount of memory for the virtual machine running MongoDB. This ensures that the balloon will not be inflated in the local operating system if there is memory pressure in the hypervisor due to an overcommitted configuration.

When using MongoDB with KVM, ensure that the CPU reservation does not exceed more than 2 virtual CPUs per physical core.

## Performance Monitoring

### iostat

On Linux, use the`iostat`command to check if disk I/O is a bottleneck for your database. Specify a number of seconds when running iostat to avoid displaying stats covering the time since server boot.

For example, the following command will display extended statistics and the time for each displayed report, with traffic in MB/s, at one second intervals:

```
iostat -xmt 
1
```

Key fields from`iostat`:

* `%util`
  : this is the most useful field for a quick check, it indicates what percent of the time the device/drive is in use.
* `avgrq-sz`
  : average request size. Smaller number for this value reflect more random IO operations.

### bwm-ng

[bwm-ng](http://www.gropp.org/?id=projects&sub=bwm-ng)is a command-line tool for monitoring network use. If you suspect a network-based bottleneck, you may use`bwm-ng`to begin your diagnostic process.

## Backups

To make backups of your MongoDB database, please refer to[MongoDB Backup Methods Overview](https://docs.mongodb.com/manual/core/backups/).

## Additional Resources

* [Blog Post: Capacity Planning and Hardware Provisioning for MongoDB In Ten Minutes](https://www.mongodb.com/blog/post/capacity-planning-and-hardware-provisioning-mongodb-ten-minutes?jmp=docs)
* [Whitepaper: MongoDB Multi-Data Center Deployments](http://www.mongodb.com/lp/white-paper/multi-dc?jmp=docs)
* [Whitepaper: Security Architecture](https://www.mongodb.com/lp/white-paper/mongodb-security-architecture?jmp=docs)
* [Whitepaper: MongoDB Architecture Guide](https://www.mongodb.com/lp/whitepaper/architecture-guide?jmp=docs)
* [Presentation: MongoDB Administration 101](http://www.mongodb.com/presentations/webinar-mongodb-administration-101?jmp=docs)
* [MongoDB Production Readiness Consulting Package](https://www.mongodb.com/products/consulting?jmp=docs#s_product_readiness)



