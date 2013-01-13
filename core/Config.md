# Config

Every actionHero node needs a `config.js` file to set things up.  [Here is a documented an example](https://github.com/evantahler/actionHero/blob/master/config.js).

When launching actionHero you can specify which config file to use with `--config=/path/to/file`, otherwise `confg.js` will be used from your working directory.  This is how to implement "environments" in actionHero.

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
	
	// start the server!
	actionHero.start(params, function(err, api){
		api.log("Boot Successful!");
	});

```
