#Actions

## General

The core of actionHero is the Action framework, **actions** are the basic units of work.  All connection types from all servers can use them generically.  The goal of an action is to set the `connection.response` ( and `connection.error` when needed) values to build the response to the client.

You can create you own actions by placing them in a `./actions/` folder at the root of your application.  You can use the generator with `actionHero generateAction`

Actions are called directly by clients, or by [tasks](https://github.com/evantahler/actionHero/wiki/Tasks) which can invoke them in the background

Here's an example of a simple action which will return a random number to the client:

```javascript

	var action = {};
	
	/////////////////////////////////////////////////////////////////////
	// metadata
	action.name = "randomNumber";
	action.description = "I am an API method which will generate a random number";
	action.inputs = {
		"required" : [],
		"optional" : []
	};
	action.outputExample = {
		randomNumber: 123
	}
	
	/////////////////////////////////////////////////////////////////////
	// functional
	action.run = function(api, connection, next){
		connection.response.randomNumber = Math.random();
		next(connection, true);
	};
	
	/////////////////////////////////////////////////////////////////////
	// exports
	exports.action = action;
```

or more concisely: 


```javascript

	exports.action = {
	  name: "randomNumber",
	  description: "I am an API method which will generate a random number",
	  inputs: { required: [], optional: [] },
	  outputExample: { randomNumber: 123 },
	  run:function(api, connection, next){
		connection.response.randomNumber = Math.random();
		next(connection, true);
	  }
	}

```

You can also define more than one action per file if you would like:

```javascript

    var commonInputs = {
    	required: ['email', 'password'],
    	optional: []
    };

    exports.userAdd = {
    	name: 'userAdd',
    	description: 'i add a user',
    	inputs: commonInputs,
    	outputExample: {},
    	run: function(api, connection, next){
    		// your code here
    		next(connection, true);
    	}
    };
    
    exports.userDelete = {
    	name: 'userDelete',
    	description: 'i delete a user',
    	inputs: commonInputs,
    	outputExample: {},
    	run: function(api, connection, next){
    		// your code here
    		next(connection, true);
    	}
    }
```

## Versions

ActionHero supports multiple versions of the same action.  This will allow you to support actions/routes of the same name with upgraded functionality.

- actions optionally have the `action.version` attribute
- a reserverd param, `apiVersion` is used to directly specify the version of an action a client may request
- if a client doesn't specify an `apiVersion`, they will be directed to the higest numerical version of that action
- you can optionally create routes to handle your API versioning:

```javascript
exports.routes = {
  all: [
    // creates routes like `/api/myAction/1/` and `/api/myAction/2/`
    // will also default `/api/myAction` to the latest version
    { path: "/myAction/:apiVersion", action: "myAction" },

    // creates routes like `/api/1/myAction/` and `/api/2/myAction/`
    // will also default `/api/myAction` to the latest version
    { path: "/:apiVersion/myAction", action: "myAction" },
  ]
};

```

## Notes

* Actions are asynchronous, and require in the API object, the connection object, and the callback function.  Completing an action is as simple as calling `next(connection, toRender)`.  The second param in the callback is a boolean to let the framework know if it needs to render anything else to the client.  There are some actions where you may have already sent the user output (see the `file.js` action for an example) where you would not want to render the default messages.
* The metadata is used in reflexive and self-documenting actions in the API, such as `actionsView`.  
* You can limit how many actions a persistant client (websocket, tcp, etc) can have pending at once with `api.configData.general.simultaniousActions`
* `actions.inputs.required` and `actions.inputs.optional` are used for both documentation and for building the whitelist of allowed parameters the API will accept.  Client params not included in these lists will be ignored for security.
* actionHero strives to keep the `connection` object uniform among various client types.  All connections have the `connection.response` and `connection.error` objects.  You can inspect `connection.type` to learn more about the connection.  The gory details of the connection (which vary on its type) are stored in `connection.rawConnection` which will contain the websoctket, tcp connection, etc.  For web clients, `connection.rawConnection = {req: req, res: res}`  
* For web connections, you do have `connection.method` which will be "GET", "POST", etc and `connection.cookies`.  

[You can learn more about handling HTTP verbs and file uploads here](https://github.com/evantahler/actionHero/wiki/web) and [TCP Clients](https://github.com/evantahler/actionHero/wiki/socket) and [Web-Socket Clients](https://github.com/evantahler/actionHero/wiki/websocket)