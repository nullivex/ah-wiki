## Requirements
* node.js ( >= v0.6.0 required but >= v0.8.0 recommended)
* npm
* redis (for actionCluster)

## Install & Quickstart

**Get Started Now:**

	npm install actionHero -g
	actionHero generate
	actionHero start

* Create a new directory `mkdir ~/project && cd ~/project`
* Checkout the actionHero source `npm install actionHero`
* Use the generator to create a template project `./node_modeules/bin/actionHero generate`
* We will be running your new application, and you can start up the server: `./node_modeules/bin/actionHero start`

Visit `http://127.0.0.1:8080` in your browser and telnet to `telnet localhost 5000` to see the actionHero in action!
	
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
	|- pids
	|-- (pidfiles for your running servers)
	|
	|- public
	|-- (your static assets to be served by /file)
	|
	|- tasks
	|-- (your tasks)
	|
	readme.md
	routes.js
	config.js
	package.json (be sure to include 'actionHero':'x')
