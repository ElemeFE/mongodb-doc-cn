# Priority 0 Replica Set Members

On this page

* [Priority 0 Members as Standbys](https://docs.mongodb.com/manual/core/replica-set-priority-0-member/#priority-0-members-as-standbys)
* [Priority 0 Members and Failover](https://docs.mongodb.com/manual/core/replica-set-priority-0-member/#priority-0-members-and-failover)
* [Configuration](https://docs.mongodb.com/manual/core/replica-set-priority-0-member/#configuration)

A_priority 0_member is a secondary that**cannot**become[primary](https://docs.mongodb.com/manual/reference/glossary/#term-primary)._Priority 0_members cannot_trigger_[elections](https://docs.mongodb.com/manual/reference/glossary/#term-election). Otherwise these members function as normal secondaries. A_priority 0_member maintains a copy of the data set, accepts read operations, and votes in elections. Configure a_priority 0_member to prevent[secondaries](https://docs.mongodb.com/manual/reference/glossary/#term-secondary)from becoming primary, which is particularly useful in multi-data center deployments.

For example, in the following diagram, one data center hosts the primary and a secondary. A second data center hosts one_priority 0_member that cannot become primary.

![](https://docs.mongodb.com/manual/_images/replica-set-three-members-geographically-distributed.bakedsvg.svg "Diagram of a 3 member replica set distributed across two data centers. Replica set includes a priority 0 member.")

## Priority 0 Members as Standbys

A_priority 0_member can function as a standby. In some replica sets, it might not be possible to add a new member in a reasonable amount of time. A standby member keeps a current copy of the data to be able to replace an unavailable member.

In many cases, you need not set standby to_priority 0_. However, in sets with varied hardware or[geographic distribution](https://docs.mongodb.com/manual/core/replica-set-architecture-geographically-distributed/#replica-set-geographical-distribution), a_priority 0_standby ensures that only qualified members become primary.

A_priority 0_standby may also be valuable for some members of a set with different hardware or workload profiles. In these cases, deploy a member with_priority 0_so it can’t become primary. Also consider using an[hidden member](https://docs.mongodb.com/manual/core/replica-set-hidden-member/#replica-set-hidden-members)for this purpose.

If your set already has seven voting members, also configure the member as[non-voting](https://docs.mongodb.com/manual/core/replica-set-elections/#replica-set-non-voting-members).

## Priority 0 Members and Failover

When configuring a_priority 0_member, consider potential failover patterns, including all possible network partitions. Always ensure that your main data center contains both a quorum of voting members and contains members that are eligible to be primary.

## Configuration

To configure a_priority 0_member, see[Prevent Secondary from Becoming Primary](https://docs.mongodb.com/manual/tutorial/configure-secondary-only-replica-set-member/).

