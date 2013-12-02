# Running actionHero

## The actionHero Binary
The main way to run your actionHero server is to use the included `./node_modules/.bin/actionHero` binary.  Note that there is no `main.js` or specific start script your project needs.  actionHero handles this for you.  Your actionHero project simply needs to follow the proper directory conventions and it will be runnable.

At the time of this writing the actionHero binary's help contains:

```bash
Binary options:
* help (default)
* start
* startCluster
* generate
* generateAction
* generateTask
* generateInitializer
* generateServer

Descriptions:

* actionHero help
  will display this document

* actionHero generate
  will prepare an empty directory with a template actionHero project

* actionHero generateAction --name=[name] --description=[description] --inputsRequired=[inputsRequired] --inputsOptional=[inputsOptional]
  will generate a new action in `actions`
  [name] (required)
  [description] (required) should be wrapped in quotes if it contains spaces
  [inputsRequired] (optional) should be a comma-sepperated list IE: "userName,password"
  [inputsOptional] (optional) should be a comma-sepperated list IE: "userName,password"

* actionHero generateTask --name=[name] --description=[description] --scope=[scope] --frequency=[frequency] 
  will generate a new task in `tasks`
  [name] (required)
  [description] (required) should be wrapped in quotes if it contains spaces
  [scope] (optional) can be "any" or "all"
  [frequency] (optional)

* actionHero generateInitializer --name=[name]
  will generate a new initializer in `initializers`
  [name] (required)

* actionHero generateServer --name=[name]
  will generate a new server in `servers`
  [name] (required)

* actionHero start --config=[/path/to/config.js] --title=[processTitle]  --daemon
  will start a template actionHero server
  this is the respondant to `npm start`
  [config] (optional) path to config.js, defaults to `process.cwd() + "/" + config.js`. You can also use ENV[ACTIONHERO_CONFIG].
  [title] (optional) process title to use for actionHero-s ID, ps, log, and pidFile defaults. Must be unique for each member of the cluster.  You can also use ENV[ACTIONHERO_TITLE]. Process renaming does not work on OSX/Windows
  [daemon] (optional) to fork and run as a new background process defalts to false

* actionHero startCluster --exec=[command] --workers=[numWorkers] --pidfile=[path] --log=[path] --title=[clusterTitle] --workerTitlePrefix=[prefix] --config=[/path/to/config.js]  --daemon
  will launch a actionHero cluster (using node-s cluster module)
  [exec] (optional) command for the cluster-master to run to stat the workers
  [workers] (optional) number of workers (defaults to # CPUs - 2)
  [pidfile] (optional) pidfile localtion (defaults to process.cwd() + /pids)
  [log] (optional) logfile (defaults to process.cwd() + /log/cluster.log)
  [title] (optional) default actionHero-master  Does not work on OSX/Windows
  [workerTitlePrefix] (optional) default actionHero-worker
  [config] (optional) path to config.js for workers, defaults to `process.cwd() + "/" + config.js`. You can also use ENV[ACTIONHERO_CONFIG]
  [daemon] (optional) to fork and run as a new background process defalts to false

More Help & the actionHero wiki can be found @ http://actionherojs.com
```

## Linking the actionHero binary

* If you installed actionHero globally (`npm install actionHero -g`) you should have the `actionHero` binary available to you within your shell at all times.
* Otherwise, you can reference the binary from either `./node_modules/.bin/actionHero` or `./node_modules/actionHero/bin/actionHero`.
* If you installed actionHero locally, you can add a reference to your path (OSX and Linux): `export PATH=$PATH:node_modules/.bin` to be able to use simpler commands, IE `actionHero start`. On windows this can be done by running `set PATH=%PATH%;%cd%\node_modules\.bin` at command prompt (not powershell).

## Programatic Use of actionHero

While **NOT** encouraged, you can always instantiate an actionHero server yourself.  Perhaps you wish to combine actionHero with an existing project.  Here's how!  Take note that using these methods will not work for actionCluster, and only a single instance will be started within your project.  

Feel free to look at the source of `./node_modules/actionHero/bin/include/start` to see how the main actionHero server is implemented for more information.

You can programmatically control an actionHero server with `actionHero.start(params, callback)`, `actionHero.stop(callback)` and `actionHero.restart(callback)`

```javascript

    var actionHeroPrototype = require("actionHero").actionHeroPrototype;
    var actionHero = new actionHeroPrototype();

	var timer = 5000;
	actionHero.start(params, function(err, api){
		
		api.log(" >> Boot Successful!");
		setTimeout(function(){
			
			api.log(" >> restarting server...");
			actionHero.restart(function(err, api){
				
				api.log(" >> Restarted!");
				setTimeout(function(){
					
					api.log(" >> stopping server...");
					actionHero.stop(function(err, api){
						
						api.log(" >> Stopped!");
						process.exit();
						
					});
				}, timer);
			})
		}, timer);
	});
```

## Jake

actionHero integrates with [jake](https://github.com/mde/jake/) (javascript make) to allow you to tun manual jake tasks within actionHero's environment.  Those of you coming from Ruby, jake is very similar to `rake`.

To use jake, you should install jake globally `npm install -g jake` or you can use it locally by adding it to your `package.json`

actionHero will generate a few example jake tasks in `./jakelib/actionHero.jake` to help you save and restore the cache and manage tasks.  Most importantly, the `actionHero:envrionment` task is defined for you.  Require this in all of you tasks so you can have access to the `api` object within your tasks.  You will have access to all of your initializers, actions, and tasks within the api object.  `api` will represent an initialized sever, but not a started one.  Your initializer's "_start" methods will not be run, nor will the servers be started.  For example:

```javascript
desc("my actionHero jake task");
task("myTask", {async: true}, function(){
  var api = jake.Task["actionHero:envrionment"].value;
  // your logic here
  complete(process.exit());
});
```

As always, you can list your project's jake tasks with `jake -T`

```bash
> jake -T

jake actionHero:environment                     # I will load and init an actionHero environment
jake actionHero:actions:list                    # List your actions and metadata
jake actionHero:tasks:enqueueAllPeriodicTasks   # This will enqueue all periodic tasks (could lead to duplicates)
jake actionHero:tasks:enqueuePeriodicTask       # This will enqueue a periodic task
jake actionHero:tasks:stopPeriodicTask          # This will remove an enqueued periodic task
jake actionHero:redis:flush                     # This will clear the entire actionHero redis database
jake actionHero:cache:clear                     # This will clear actionHero's cache
jake actionHero:cache:dump                      # This will save the current cache as a JSON object
jake actionHero:cache:load                      # This will load (and overwrite) the cache from a file
```

You can [learn more about jake here](https://github.com/mde/jake/).
You can [view actionHero's default jake tasks here](https://github.com/evantahler/actionHero/blob/master/jakelib/actionHero.jake)

## Signals

actionHero is intended to be run on *nix operating systems.  The `start` and `startCluster` commands provide support for signaling. (There is limited support for some of these commands in windows).

**actionHero start**

- `kill` / `term` / `int` : Process will attempt to "gracefully" shut down.  That is, the worker will close all server connections (possibly sending a shutdown message to clients, depending on server type), stop all task workers, and eventually shut down.  This action may take some time to fully complete.
- `USR2`: Process will restart itself.  The process will preform the "graceful shutdown" above, and they restart.

**actionHero startCluster**

All signals should be sent to the cluster master process.  You can still signal the termination of a worker, but the cluster manager will start a new one in its place.

- `kill` / `term` / `int`:  Will signal the master to "gracefully terminate" all workers.  Master will terminate once all workers have completed
- `USR2` / `HUP` : "Hot reload".  Worker will kill off existing workers one-by-one, and start a new worker in their place.  This is used for 0-downtime restarts.  Keep in mind that for a short while, your server will be running both old and new code while the workers are rolling.
- `WINCH`: "Gracefully terminate" all workers.  Only the master will remain running
- `TTOU`: remove one worker
- `TTIN`: add one worker

## Windows-Specific Notes

- Sometimes actionHero may require a git-based module (rather than a NPM module).  You will need to have git installed.  Depending on how you installed git, it may not be available to the node shell.  Be sure to have also installed references to git.  You can also run node/npm install from the git shell. 
- Sometimes, npm will not install the actionHero binary @ `/node_modules/.bin/actionHero`, but rather it will attempt to create a windows executable and wrapper.  You can launch actionHero directly with `./node_modules/actionHero/bin/actionHero`
