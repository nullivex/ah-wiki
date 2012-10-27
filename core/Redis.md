## General Redis

ActionHero makes use of Redis for number of things when running in 'actionCluser' mode.  The use of redis is not required to start an actionHero cluster, but is required to enable many of its advanced features.

In your `config.json` you can define which redis database you use.  Redis allows you to have multiple databases per server, so you may store data for many actionClusters on one server, although this is not recommended. In redis, the pub/sub system is global, so it is a good practice to prefix your channel names with your `api.configData.redis.DB` so that you will not see conflicts.  

## Persistant and Shared Cache

When using redis, actionHero's [cache functions](https://github.com/evantahler/actionHero/wiki/Cache) upgrade to store and retrieve data from redis.  This means that each member of an actionCluster can have accesses to the same data.  You can also use redis' persistence functions to ensure your cache is saved to disk as needed.  Redis is single threaded, so there will be first-ing-first-out queue of data storage if many peers try to write to the same cache-key at the same time.

## Shared Task Queue

actionHero's tasks queue is extended to be a redis list so that any peer can work off a task without fear of duplication.  While working on a task, each peer moves the task to a personal queue.  

## Chat System

[The chat sub-system](https://github.com/evantahler/actionHero/wiki/Chat) works using redis' pub/sub system to share data between all nodes in the actionCluster.  This enables very fast communication between peers and nodes.

## Storage and retrieval

Upon initialization, `api.redis.client` will be made available to you, and is [an instance of the node.js redis library](https://npmjs.org/package/redis).  See their docs for how to use it.  This library is callback-safe, so you can use it for your own needs, and will not conflict with actionHero's core use of it.  It is recommended you use `api.redis.client` rather than creating your own connection.

## Pub/Sub

actionHero provides wrappers around the redis pub/sub system so that you can easily communicate will all nodes in your cluster.  `api.redis.client` is used to publish events, but another connection, `api.redis.clientSubscriber` is used to catch events (as a single redis connection cannot be used to both publish and subscribe).  Do not use `api.redis.clientSubscriber` to manipulate data.

You can publish data with `api.redis.client.publish(channel, message)`.  `message` must respond to `JSON.sringify`.

`api.redis.registerChannel(api, channel, handler)` is used to define a callback to a redis pub/sub event, where `channel` is the name of the channel you want to subscribe to, and `handler` is the callback function to be called when an event is received.

`handler` receives `(channel, message)`, where message is whatever data was published.

A full example to publish a message and catch it would be:

```javascript

	var testChannel = "testChannel:" + api.configData.redis.DB;
	
	api.redis.registerChannel(api, testChannel, function(channel, message){
	  api.log("A message from" + message.person);
	  api.log(message.body);
	});
	
	var payload = {
		person: "Evan",
		body: "Hi!"
	};
	api.redis.client.publish(testChannel, payload)

```

## Keys and Channels

actionHero will create the following stores within your redis database:

**Keys**

- `actionHero:peers` [] a list of all the peers in the action cluster.  New members add themselves to it
- `actionHero:peerPings` {} a hash of the last ping time of each peer member.  Useful to check if a peer has gone away
- `actionHero:tasks` [] a list of tasks to be completed.  Any memeber can push to the queue; all workers will pull one at a time from the queue
- `actionHero:tasks:{serverID}` [] a list of tasks to be completed by only this node.  This queue will be drained at a lower priority than the regular task queue
- `actionHero:tasksClaimed` [] a list of tasks being either worked on or sleeping by a node.
- `actionHero:cache` [] the common shared cache object
- `actionHero:stats` [] the common shared stats object
- `actionHero:roomMembers-{roomName}` [] a list of the folks in a given socket room
- `actionHero:webMessages:{client.id}` [] an array of messages for a http(s) client (expires)

**Channels**

- `actionHero:say:[db]` the pub/sub channel used for the chat sub-system