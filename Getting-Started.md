## Requirements
* node.js ( >= v0.8.0)
* npm
* redis (for cluster support, cache, stats, and tasks)

## Install & Quickstart

**Get Started Now:**

```bash
npm install actionHero
./node_modules/.bin/actionHero generate
npm start
```

* Create a new directory `mkdir ~/project && cd ~/project`
* Checkout the actionHero source `npm install actionHero`
* Use the generator to create a template project `./node_modeules/.bin/actionHero generate`
* You can now start up the server: `npm start`

Visit `http://127.0.0.1:8080/public` in your browser to see the actionHero in action!

You can also opt to install actionHero globally `npm install actionHero -g` and then you can just call `actionHero start`.
	
## Application Structure

Actions in `/actions` will be loaded in automatically, along `/initializers` and `/tasks`. `/public` will become your application's default static asset location.  The map below describes actionHero's default project layout.  If you wish to customize your project's paths, you can do so within `config.js` in the `api.configData.general.paths` section

	/
	|- actions
	|-- (your actions)
	|
	|- initializers
	|-- (any additional initializers you want)
	|
	|- log
	|-- (default location for logs)
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
	|- servers
	|-- (custom servers you may make)
	|
	|- tasks
	|-- (your tasks)
	|
	readme.md
	routes.js
	config.js
	Jakefile.js
	package.json (be sure to include 'actionHero':'x')

## Tutorial
Want to see an example application using actionHero?  You can check out the code and follow the detailed guide [here](https://github.com/evantahler/actionHero-tutorial).  This project demonstrates many of the core features of actionHero in a simple project.