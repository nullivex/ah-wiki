## Stats

actionHero ships with helper functions for you to store and retrieve stats about your application. When running in an actionCluster (redis enabled), 2 collections of stats will be built: global and local.  This means you can count up the requests a single actionHero node has been processing, and the total for the cluster, etc.  

Many of the core actionHero features (cache, web/tcp/websocket servers, etc) are instrumented with stats, but you are encouraged to add more!

### api.stats.increment(api, key, count, next)
- next(err, wasSet)
- key is a string
- count is a signed integer
- - this method will work on local and global stats

### api.stats.set(api, key, count, next)
- next(err, wasSet)
- key is a string
- count is a signed integer
- this method will only work on local stats

### api.stats.get(api, key, collection, next)
- next(err, data)
- key is a string
- collection is either:
  - `api.stats.collections.local`
  - `api.stats.collections.global`

### api.stats.getAll(api, next)
- next(err, stats)
- stats is a hash of `{global: globalStats, local: localStats}`

## Notes
- in `api.stats.increment`, the count can be negative or positive
- `api.stats.set` will only apply to 'local' stats
- When choosing a 'collection' for `api.stats.get`, it's easiest to use the predefined collections:
  - `api.stats.collections.local`
  - `api.stats.collections.global`
  
## Example: 
normal use:

	api.stats.increment(api, 'myCount', 2);
	api.stats.increment(api, 'myCount', 3);
	api.stats.increment(api, 'myCount', -1);
	
	api.stats.get(api, 'myCount', api.stats.collections.local, function(err, count){
		console.log(count)); // count => 4
	});
	api.stats.get(api, 'myCount', api.stats.collections.global, function(err, count){
		console.log(count)); // count => 4
	});

counts modified by `set`:

	api.stats.set(api, 'myCount', 0, next)
	
	api.stats.get(api, 'myCount', api.stats.collections.local, function(err, count){
		console.log(count)); // count => 0
	});
	api.stats.get(api, 'myCount', api.stats.collections.global, function(err, count){
		console.log(count)); // count => 4
	});