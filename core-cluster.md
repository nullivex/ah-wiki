# actionCluster

actionHero can be run either as a stand-alone server or as part of a cluster.  The goal of actionCluster is to allow you to create a group of servers which will share memory state and all be able to handle requets and run tasks.  You can also add and remove nodes from the cluster without fear of data loss or task duplication.  You can run many instances of actionHero using node.js' cluster methods.

Using a [redis](http://redis.io/) backend, actionHero nodes can now share memory objects and have a common queue for tasks.  Philosophically, we have changed from a mesh network (actionHero versions prior to v2) to a queue-based network (action hero after version 2).

When working within an actionCluster the `api.cache` methods described above switch from using an in-process memory store, to using a common one based on redis.  This means that all peers will have access to all data stored in the cache.  The task system described below also becomes a common queue which all peers will work on draining.  There should be no changes needed to your use of the api to use the benefits of cluster deployment and synchronization.  Using a redis-based backend works for both a cluster hosted on many physically separate hosts or if you set using the [node.js cluster module](https://github.com/evantahler/actionHero/blob/master/actionHeroCluster) on one host, or both at the same time.

*There have recently been significant changes to the cluster system since v1.x, please checkout the change-log if you are upgrading from an older version.*