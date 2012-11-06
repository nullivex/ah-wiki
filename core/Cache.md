## General Cache Notes

actionHero ships with the functions needed for an in-memory key-value cache.  If you are running a stand-alone version of actionHero, this cache will be in memory of the actionHero process, otherwise this data will be stored in redis.  You can cache strings, numbers, arrays and objects (anything that responds to `JSON.stringify`).

*Note:* Keys starting with an "_" should not be used, as they are in use by core parts of the system, such as the task queue.

## Methods

### `api.cache.save`

* Invoke: `api.cache.save(api, key, value, expireTimeMS, next)`
	* `expireTimeMS` can be null if you never want the object to expire 
* Callback: `next(bool)`
	* will be true unless the object could not be saved (perhaps out of ram or a bad object type).
	* overwriting an existing object will return `true`
	
`api.cache.save` is used to both create new entires or update existing cache entires.  If you don't define an expireTimeMS, `null` will be assumed, and using `null` will cause this cached item to not expire.  Objects will not be returned if they have expired, although they will no be removed from the cache store.  

There is an [example task provided you can use to periodically free up expired cache entries](https://github.com/evantahler/actionHero/blob/master/examples/tasks/cleanOldCacheObjects.js).

### `api.cache.load`

* Invoke: `api.cache.load(api, key, next)`
* Callback: `next(value, expireTimestamp, createdAt, readAt)`
	* value will be the object which was saved and null if the object cannot be found or is expired
	* expireTimestamp(ms) is when the object is set to expire in system time
	* createdAt(ms) is when the object was created
	* readAt(ms) is the timestamp at which the object was last read with `api.cache.load`.  Useful for telling if another worker has consumed the object recently
	
	If the key requested is not found (or expired), all values returned will be null.

### `api.cache.destroy`

* Invoke: `api.cache.destroy(key)`
* Callback: `next(bool)`
	* will be false if the object cannot be found, and true if destroyed
	
You can see an example of using the cache within an action in `[actions/cacheTest.js](https://github.com/evantahler/actionHero/blob/master/actions/cacheTest.js)`

## Redis

When running in a cluster, the cache system upgrades to be shared across all peers, [and is based on redis.
](https://github.com/evantahler/actionHero/wiki/Redis)

The timestamps regarding `api.cahce.load` are to help clients understand if they are working with data which has been modified by another peer (when running in an actionCluster).  There are more details on [how redis works with actionHero here.](https://github.com/evantahler/actionHero/wiki/Redis)