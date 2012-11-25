## Requirements
* node.js ( >= v0.6.0 required but >= v0.8.0 recommended)
* npm
* redis (for actionCluster)

## Install & Quickstart

**Get Started Now:**

	npm install actionHero
	npm run-script actionHero generate
	npm start

* Create a new directory `mkdir ~/project && cd ~/project`
* Checkout the actionHero source `npm install actionHero`
* Use the generator to create a template project `npm run-script actionHero generate`
* We will be running your new application from the generated `app.js`
* Start up the server: `npm start`

Visit `http://127.0.0.1:8080` in your browser and telnet to `telnet localhost 5000` to see the actionHero in action!

You can programmatically control an actionHero server with `actionHero.start(params, callback)`, `actionHero.stop(callback)` and `actionHero.restart(callback)`

```javascript

	var timer = 5000;
	actionHero.start(params, function(err, api){
		
		api.log(" >> Boot Successful!");
		setTimeout(function(){
			
			api.log(" >> restarting server...");
			actionHero.restart(function(){
				
				api.log(" >> Restarted!");
				setTimeout(function(){
					
					api.log(" >> stopping server...");
					actionHero.stop(function(){
						
						api.log(" >> Stopped!");
						process.exit();
						
					});
				}, timer);
			})
		}, timer);
	});
```
	
## Application Structure

Actions in /actions will be loaded in automatically, along /initializers and /tasks. /public will become your application's default static asset location.  You can make your own config.json in your application root with only the partial changes you want to use over the default settings.

	/
	|- actions
	|-- (your actions)
	|
	|- certs
	|-- (your https certs for your domain)
	|
	|- initializers
	|-- (any additional initializers you want)
	|
	|- log
	|-- (default location for logs)
	|
	|- initilizers
	|-- (your initilizers, loaded in before project boot)
	|
	|- node_modules
	|-- (your modules, actionHero should be npm installed in here)
	|
	|- public
	|-- (your static assets to be served by /file)
	|
	|- tasks
	|-- (your tasks)
	|
	config.js
	package.json (be sure to include 'actionHero':'x')
