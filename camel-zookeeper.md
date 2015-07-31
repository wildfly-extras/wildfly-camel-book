## camel-zookeeper

The [camel-zookeeper](http://camel.apache.org/zookeeper.html) component allows interaction with a ZooKeeper cluster and exposes the following features to Camel:
Creation of nodes in any of the ZooKeeper create modes.
Get and Set the data contents of arbitrary cluster nodes. (data being set must be convertable to byte[])
Create and retrieve the list the child nodes attached to a particular node.
A Distributed RoutePolicy that leverages a Leader election coordinated by ZooKeeper to determine if exchanges should get processed.