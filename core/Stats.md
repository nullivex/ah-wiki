## Stats

actionHero ships with helper functions for you to store and retrieve stats about your application. When running in an actionCluster, these stats are shared through the whole cluster

### api.stats.increment(api, key, count, next)
- next(err, wasSet)
- key is a string
- count is a signed integer

### api.stats.get(api, key, next)
- next(err, data)
- key is a string

### api.stats.load(api, next)
- next(err, fullStatsData)
- i get a predefined collection of stats objects