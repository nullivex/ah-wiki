Every actionHero node needs a `config.js` file to set things up.  [Here is a documented an example](https://github.com/evantahler/actionHero/blob/master/config.js).

You can overwrite these settings when you start your actionHero worker pragmatically as well by passing `configChanges` to the initFunction.   

```javascript

	var actionHero = require("actionHero").actionHero;

	// if there is no config.js file in the application's root, then actionHero will load in a collection of default params.  You can overwrite them with params.configChanges
	var params = {};
	params.configChanges = {
		general: {},
		log: {
			logFile: "api_peer_2.log",
		},
		httpServer: {
			port: 8081,
		},
		httpsServer: {
			port: 4444,
		},
		tcpServer: {
			port: 5001
		}
	}
	
	// any additional functions you might wish to define to be globally accessible can be added as part of params.initFunction.  The api object will be available.
	params.initFunction = function(api, next){
		next();
	}
	
	// start the server!
	actionHero.start(params, function(err, api){
		api.log("Boot Successful!");
	});

```
