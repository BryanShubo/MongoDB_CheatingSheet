#### Replica set of Nodes

#####1. Introduction to replication

* Availability
* Fault Tolenrence

1.  Replica set: Primary, secondary, secondary (minimal number of nodes is 3)
2.  App -> primary
3.  primary is down. Secondaries do election.
4.  App -> new primary
5.  If old primary is back, it is a secondary now.


#####2. Types of Replica Set Nodes

* Regular (primary or secondary)
* Arbiter (voting purpose)
* Delaged / Regular (can't be primary node but participate voting, p = 0)
* Hidden (Never be primary but participate voting, p = 0)

Every node has one vote.

#####3. Write Consistency
* App can write only to primary, but read from both primary and secondary.
* Primary and secondary is asynchronised copying data. So if app reads data from secondary node, it may miss some data.
* Eventual consistency (mongodb does not support this): if primary is down, app can't write any data to node during secondaries eletion. 'During the time when failover is occurring, cannot write successfully complete'

#####4. Creating a replica set
```
rs.status() // replica set status
rs.slaveOk() // reading from secondary
rs.isMaster() // check node if it is a master
```

#####5. Failover and Rollback
* It may take 3s to elect a new primary.
* If a node comes back as a secondary node, the entire dataset will be copied from the primary.
* w=1, j=1 guarantee that an insert, update, or delete has been persisted to disk.

Network Errors: in this case, you won't get any acknowledgement so you are not sure the write is persisted or not. 
```
What are the reasons why an application may receive an error back even if the write was successful. Check all that apply.

* The network TCP network connection between the application and the server was reset between the time of the write and the time of the getLastError call. 
* The MongoDB server terminates between the write and the getLastError call. 
* The network fails between the time of the write and the time of the getLastError call
```

#####6. Read Preference
```
primary // default
secondary 
secondary preference
primary preference
nearst // choose the ping time shortest one.
```

#####7. Sharding
* range based
* sharding key // immutable
* Index the shard key
* shard key has to be specified
* No shard key means search gather
* No unique key, unless it is part of shard key

```
Suppose you wanted to shard the zip code collection after importing it. You want to shard on zip code. What index would be required to allow MongoDB to shard on zip code?

An index on zip or a non-multi-key index that starts with zip.
```

#####8. Sharding and Replication

Choosing a shard key

* Sufficient cardinality
* Hotspoting // like time stamp, it is increasing automatically.










