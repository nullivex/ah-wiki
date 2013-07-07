# Running Commands

**actionHero run**

`actionHero run` a way to run internal methods from the command line.  This is similar to Ruby's `rake` command.  The `actionHero run` command has only the `api` object in scope.  

For example, 

- we can set a cache object: `actionHero run --method="api.cache.save" --args=test,123`
- then read it back: `actionHero run --method="api.cache.load" --args=test`

``` bash
> ./node_modules/.bin/actionHero run --method="api.cache.save" --args=test,123
info: actionHero >> run
info: project_root >> /Users/evantahler/PROJECTS/actionHero/
info: actionHero_root >> /Users/evantahler/PROJECTS/actionHero/
{ '0': null, '1': true }

> ./node_modules/.bin/actionHero run --method="api.cache.load" --args=test
info: actionHero >> run
info: project_root >> /Users/evantahler/PROJECTS/actionHero/
info: actionHero_root >> /Users/evantahler/PROJECTS/actionHero/
{ '0': null,
  '1': '123',
  '2': null,
  '3': 1373154368728,
  '4': 1373154385785 }
```

The response of the `run` command will be an ordered list of all the arguments passed to the callback (if it has one), or the function's return value (if the method is synchronous).

For safety, you can not run arbitrary code from `actionHero run`, so you will need to define first define the method you wish to call in an initializer, and append it to the `api` object

From actionHero Help:

```bash
* actionnHero run --command=[method] --args=[args] --log=[log]
  will run javascipt provided in `command`
  [method] (required)
  [args] (optional) argumets the the method.  comma seperated list
  [log] (optional) use the logger defaults?
```