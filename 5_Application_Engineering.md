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



