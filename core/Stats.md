## Stats

actionHero ships with stats backend (in redis) to store and retrieve stats about your application.  

Many of the core actionHero features (cache, web/tcp/websocket servers, etc) are instrumented with stats, but you are encouraged to add more!

### api.stats.increment(key, count)
- key is a string of the form ("thing:stuff")
- count is a signed integer

### api.stats.get(key, collection, next)
- next(err, data)
- key is a string of the form ("thing:stuff")
- collection (optional) is one of the collections used for stats set in `api.configData.stats.keys`

### api.stats.getAll(collections, next)
- next(err, stats)
- collections (optional) is an array of one or more of the keys used for stats set in `api.configData.stats.keys`
- stats is a hash of `{key1: stats {}, key2: stats {} }`
- keys will be collapsed into a hash 

## Notes
- in `api.stats.increment`, the count can be negative or positive
- `api.configData.stats.witeFrequency` is how often the local stats buffer is written to redis.  Faster frequencies will keep your stats up to date faster, but slow down the rest of your server.
- you can have more than one stats hash.  For example, you might want to keep `global` cluster stats and local stats per actionHero server.  You can do this by:

```javascript
configData.stats = {
  witeFrequency: 1000,
  keys: [    
    'actionHero:stats',
    'actionHero:stats:' + api.id
  ], 
```
  
## Example: 
``` javascript
api.stats.increment('myCount', 2);
api.stats.increment('myCount', 3);
api.stats.increment('myCount', -1);

api.stats.get('myCount', function(err, count){
	console.log(count)); // count => 4
});
```