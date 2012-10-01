## General

The core of actionHero is the Action framework, **actions** are the basic units of a request and work for HTTP and socket responses.  The goal of an action is to set the `connection.response` ( and `connection.error` when needed) value to build the response to the client.

You can create you own actions by placing them in a `./actions/` folder at the root of your application.

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
	  outputExample: { randomNumber: 123 }.
	  run:function(api, connection, next){
		connection.response.randomNumber = Math.random();
		next(connection, true);
	  }
	}

```

Notes:


* Actions are asynchronous, and require in the API object, the connection object, and the callback function.  Completing an action is as simple as calling `next(connection, toRender)`.  The second param in the callback is a boolean to let the framework know if it needs to render anything else to the client.  There are some actions where you may have already sent the user output (see the `file.js` action for an example) where you would not want to render the default messages.
* The metadata is used in reflexive and self-documenting actions in the API, such as `actionsView`.  `actions.inputs.required` and `actions.inputs.optional` are used for both documentation and for building the whitelist of allowed parameters the API will accept.  
* The connection object will change based on the type of connection it is.  For example, a web client will have `connection.req` and `connection.res`, and a TCP client will not, but it will have `connection.room` and `connection.messageCount`.  All connections have the `connection.response` and `connection.error` objects.  You can inspect `connection.type` to learn more about the connection.
* For web connections, you do have `connection.method` which will be "GET"m "POST", etc.  [You can learn more about handling HTTP verbs and file uploads here](https://github.com/evantahler/actionHero/wiki/Web-Clients) and [TCP Clients](https://github.com/evantahler/actionHero/wiki/TCP-Clients) and [Web-Socket Clients](https://github.com/evantahler/actionHero/wiki/Web-Socket-Clients)