# Delayed Replica Set Members

On this page

* [Considerations](https://docs.mongodb.com/manual/core/replica-set-delayed-member/#considerations)
* [Example](https://docs.mongodb.com/manual/core/replica-set-delayed-member/#example)
* [Configuration](https://docs.mongodb.com/manual/core/replica-set-delayed-member/#configuration)

Delayed members contain copies of a[replica set’s](https://docs.mongodb.com/manual/reference/glossary/#term-replica-set)data set. However, a delayed member’s data set reflects an earlier, or delayed, state of the set. For example, if the current time is 09:52 and a member has a delay of an hour, the delayed member has no operation more recent than 08:52.

Because delayed members are a “rolling backup” or a running “historical” snapshot of the data set, they may help you recover from various kinds of human error. For example, a delayed member can make it possible to recover from unsuccessful application upgrades and operator errors including dropped databases and collections.

## Considerations

### Requirements

Delayed members:

* **Must be**
  [priority 0](https://docs.mongodb.com/manual/core/replica-set-priority-0-member/#replica-set-secondary-only-members)
  members. Set the priority to 0 to prevent a delayed member from becoming primary.
* **Should be**
  [hidden](https://docs.mongodb.com/manual/core/replica-set-hidden-member/#replica-set-hidden-members)
  members. Always prevent applications from seeing and querying delayed members.
* _do_
  vote in
  [elections](https://docs.mongodb.com/manual/reference/glossary/#term-election)
  for primary, if
  [`members[n].votes`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.members[n].votes)
  is set to 1.

### Behavior

Delayed members copy and apply operations from the source[oplog](https://docs.mongodb.com/manual/reference/glossary/#term-oplog)on a delay. When choosing the amount of delay, consider that the amount of delay:

* must be equal to or greater than your expected maintenance window durations.
* must be
  _smaller_
  than the capacity of the oplog. For more information on oplog size, see
  [Oplog Size](https://docs.mongodb.com/manual/core/replica-set-oplog/#replica-set-oplog-sizing)
  .

### Sharding

In sharded clusters, delayed members have limited utility when the[balancer](https://docs.mongodb.com/manual/reference/glossary/#term-balancer)is enabled. Because delayed members replicate chunk migrations with a delay, the state of delayed members in a sharded cluster are not useful for recovering to a previous state of the sharded cluster if any migrations occur during the delay window.

## Example

In the following 5-member replica set, the primary and all secondaries have copies of the data set. One member applies operations with a delay of 3600 seconds \(one hour\). This delayed member is also_hidden_and is a_priority 0 member_.

![](https://docs.mongodb.com/manual/_images/replica-set-delayed-member.bakedsvg.svg "Diagram of a 5 member replica set with a hidden delayed priority 0 member.")

## Configuration

A delayed member has its[`members[n].priority`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.members[n].priority)equal to`0`,[`members[n].hidden`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.members[n].hidden)equal to`true`, and its[`members[n].slaveDelay`](https://docs.mongodb.com/manual/reference/replica-configuration/#rsconf.members[n].slaveDelay)equal to the number of seconds of delay:

```js
{
   "_id" : <num>,
   "host" : <hostname:port>,
   "priority" : 0,
   "slaveDelay" : <seconds>,
   "hidden" : true
}
```

To configure a delayed member, see[Configure a Delayed Replica Set Member](https://docs.mongodb.com/manual/tutorial/configure-a-delayed-replica-set-member/).

