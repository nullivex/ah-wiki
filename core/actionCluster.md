# Runnign actionHero in a Cluster

actionHero can be run either as a stand-alone server or as part of a cluster.  The goal of these cluster helpers is to allow you to create a group of servers which will share state and all be able to handle requests and run tasks.  You can also add and remove nodes from the cluster without fear of data loss or task duplication.  You can also run many instances of actionHero on the same saerver using node.js' cluster methods (`actionHero startCluster`).

## Cache

Using a [redis](http://redis.io/) backend, actionHero nodes share memory objects (using the api.cache methods) and have a common queue for tasks. This means that all peers will have access to all data stored in the cache.  The task system also becomes a common queue which all peers will work on draining.  There should be no changes needed to your use of the api to enable the benefits of cluster deployment and synchronization.  Keep in mind that many clients/server can access a cache's value simultaneously, so build your actions carefully not to have conflicting state.

## Pub/Sub (Faye)

actionHero also uses [faye](http://faye.jcoglan.com/) to allow for pub/sub communication between node.

You can broadcast and receive messages from other peers in the cluster:

#### api.faye.client.subscribe(channel, callback)
- channel is a string of the form "/my/channel"
- callback will be passed `message` which is an object

#### api.faye.client.publish(channel, message);
- channel is a string of the form "/my/channel"
- message is an object

## Redis Key Reservations

The following keys in redis will be in use by actionHero:

- `actionHero:cache` [] the common shared cache object
- `actionHero:stats` [] the common shared stats object
- `actionHero:roomMembers-{roomName}` [] a list of the folks in a given socket room

Faye will also make use of a large number of keys, but under the "faye" namespace (prefix)

Tasks will also make use of a large number of keys, but under the "resque" namespace (prefix)