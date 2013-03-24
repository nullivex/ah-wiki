# Action Cluster

actionHero can be run either as a stand-alone server or as part of a cluster.  The goal of actionCluster is to allow you to create a group of servers which will share memory state and all be able to handle requests and run tasks.  You can also add and remove nodes from the cluster without fear of data loss or task duplication.  You can run many instances of actionHero using node.js' cluster methods.

## Cache

Using a [redis](http://redis.io/) backend, actionHero nodes share memory objects (using the api.cache methods) and have a common queue for tasks. 

When working within an actionCluster the `api.cache` methods switch from using an in-process memory store to using a common store in redis.  This means that all peers will have access to all data stored in the cache.  The task system also becomes a common queue which all peers will work on draining.  There should be no changes needed to your use of the api to enable the benefits of cluster deployment and synchronization.  Using a redis-based backend works for both a cluster hosted on many physically separate hosts or if you set using the [node.js cluster module](https://github.com/evantahler/actionHero/blob/master/actionHeroCluster) on one host, or both at the same time.  Keep in mind that many clients/server can access a cache's value simultaneously, so build your actions carefully not to have conflicting state.

## Pub/Sub (Faye)

actionHero also uses [faye](http://faye.jcoglan.com/) to allow for pub/sub communication between node.

You can broadcast and receive messages from other peers in the cluster:

#### api.faye.client.subscribe(channel, callback)
- channel is a string of the form "/my/channel"
- callback will be passed `message` which is an object

#### api.faye.client.publish(channel, message);
- channel is a string of the form "/my/channel"
- message is an object

## Redis Reservations

The following keys in redis will be in use by actionHero:

- `actionHero:peers` [] a list of all the peers in the action cluster.  New members add themselves to it
- `actionHero:peerPings` {} a hash of the last ping time of each peer member.  Useful to check if a peer has gone away
- `actionHero:cache` [] the common shared cache object
- `actionHero:stats` [] the common shared stats object
- `actionHero:roomMembers-{roomName}` [] a list of the folks in a given socket room
- `actionHero:webMessages:{client.id}` [] an array of messages for a http(s) client (expires)
- `actionHero:tasks:global` [] a list of tasks to be completed.  Any memeber can push to the queue; all workers will pull one at a time from the queue
- `actionHero:tasks:delayed` [] a list of tasks to to be completed in the future
- `actionHero:tasks:{serverID}` [] a list of tasks to be completed by only this node.  This queue will be drained at a lower priority than the regular task queue
- `actionHero:tasks:data` {} the data hash for the task queue.
- `actionHero:tasks:processing` [] a list of tasks being worked on.

Faye will also make use of a large number of keys, but under the "faye" namespace (prefix)