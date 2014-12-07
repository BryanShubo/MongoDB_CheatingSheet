#### Replica set of Nodes

#####1. Introduction to replication

* Availability
* Fault Tolenrence

1.  Replica set: Primary, secondary, secondary
2.  App -> primary
3.  primary is down. Secondaries do election.
4.  App -> new primary
5.  If old primary is back, it is a secondary now.

