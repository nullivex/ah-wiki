# Cache

actionHero ships with the functions needed for an in-memory key-value cache.  You can cache strings, numbers, arrays and objects (anything that responds to `JSON.stringify`). Cache functions:

* `api.cache.save(api, key, value, expireTimeMS, next)`
* `api.cache.load(api, key, next)`
* `api.cache.destroy(api, key, next)`


`api.cache.save` is used to both create new entires or update existing cache entires.  If you don't define an expireTimeMS.  Using `null` here will cause this cached item to not expire.  Objects will not be returned if they have expired, although they will no be removed from RAM/disc.  There is an example task provided you can use to periodically free up expired cache entries.  If you are running a stand-alone version of actionHero, this cache will be in memory of the actionHero process, otherwise this data will be stored in redis.

Note: that the keys starting with an "_" should not be used, as they are in use by core parts of the system, such as the task queue.

**api.cache.save**:

* `api.cache.save(api, key, value, expireTimeMS, next)`
	* `expireTimeMS` can be null if you never want the object to expire 
* `next(bool)`
	* will be true unless the object could not be saved (perhaps out of ram or a bad object type).
	* overwriting an existing object will return `true`

**api.cache.load**:

* `api.cache.load(api, key, next)`
* `next(value, expireTimestamp, createdAt, readAt)`
	* value will be the object which was saved
	* expireTimestamp(ms) is when the object is set to expire in system time
	* createdAt(ms) is when the object was created
	* readAt(ms) is the timestamp at which the object was last read with `api.cache.load`.  Useful for telling if another worker has consumed the object recently

**api.cache.destroy**:

* `next(bool)`
	* will be false if the object cannot be found
	
You can see an example of using the cache within an action in `[actions/cacheTest.js](https://github.com/evantahler/actionHero/blob/master/actions/cacheTest.js)`
