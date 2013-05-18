# Running actionHero

## The actionHero Binary
The main way to run your actionHero server is to use the included `./node_modules/.bin/actionHero` binary.  Note that there is no `main.js` or specific start script your project needs.  actionHero handles this for you.  Your actionHero project simply needs to follow the proper directory convert ions and it will be runable.

At the time of this writing the actionHero binary's help contains:

actionHero - a node.js API framework for both tcp sockets, web sockets, and http clients.

    Binary options:
	* help (default)
	* helpSmall
	* generate
	* generateAction
	* generateTask
	* generateInitializer
	* generateServer
	* actionHeroServer
	* actionHeroCluster
	
	Descriptions:
	
	* actionHero help
	  will display this document
	
	* actionHero helpSmall
	  will display a short (and colored) message
	  this shows upon `npm install of actionHero`
	
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
	  [config] (optional) path to config.js, defaults to `process.cwd() + "/" + config.js`
	  [title] (optional) process title to use for ps and pidFile defaults to (actionHero) Does not work on OSX/Windows
	  [daemon] (optional) to fork and run as a new background process defalts to false
	
	* actionHero startCluster --exec=[command] --workers=[numWorkers] --pidfile=[path] --log=[path] --silent=[silent] --title=[clusterTitle] --workerTitlePrefix=[prefix] --config=[/path/to/config.js]  --daemon
	  will launch a actionHero cluster (using node's cluster module)
	  [exec] (optional) command for the custer-master to run to stat the workers
	  [workers] (optional) number of workers (defaults to # CPUs - 2)
	  [pidfile] (optional) pidfile localtion (defaults to process.cwd() + /pids)
	  [log] (optional) logfile (defaults to process.cwd() + /log/cluster.log)
	  [silent] (optional) should worker stdout be sent to cluster master (default true)
	  [title] (optional) default actionHero-master  Does not work on OSX/Windows
	  [workerTitlePrefix] (optional) default actionHero-worker
	  [config] (optional) path to config.js, defaults to `process.cwd() + "/" + config.js`
	  [daemon] (optional) to fork and run as a new background process defalts to false

## Linking the actionHero binary

* If you installed actionHero globally (`npm install actionHero -g`) you should have the `actionHero` binary available to you within your shell at all times.
* Otherwise, you can reference the binary from either `./node_modules/.bin/actionHero` or `./node_modules/actionHero/bin/actionHero`.
* If you installed actionHero locally, you can add a reference to your path (OSX and Linux): `export PATH=$PATH:node_modules/.bin` to be able to use simpler commands, IE `actionHero start`

## Programatic Use of actionHero

While **NOT** encouraged, you can always instantiate an actionHero server yourself.  Perhaps you wish to combine actionHero with an existing (express?) project.  Here's how!  Take note that using these methods will not work for actionCluster, and only a single instance will be started within your project.  

Feel free to look at the source of `./node_modules/actionHero/bin/include/start` to see how the main actionHero server is implemented for more information.  Note that actionHero assumes that your project hierarchy is consistent with a stand-alone server, so ensure that `process.cwd()` matches.

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

## Windows Notes

- Sometimes actionHero may require a git-based module (rather than a NPM module).  You will need to have git installed.  Depending on how you installed git, it may not be available to the node shell.  Be sure to have also installed references to git.  You can also run node/npm install from the git shell. 
- sometimes, npm will not install the actionHero binary @ `/node_modules/.bin/actionHero`, but rather it will attempt to create a windows executable and wrapper.  You can launch actionHero directly with `./node_modules/actionHero/bin/actionHero`