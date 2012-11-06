## General

actionHero uses [socket.io](http://socket.io/) for web sockets.  Within actionHero, web sockets are bound to either the web server (either http or https).  Also, if you are using a redis backend store (which is required to use actionHero in a cluster), socket.io will be configured to use this store automatically.

Just like the additional actions added for TCP connection, web socket connections have access to the chat room methods.  A template which exposes them is available in examples and looks like this:

```javascript

	<script src="/socket.io/socket.io.js"></script>
	<script>
		var port = window.location.port
		if(port == "" || port == null){ port = "80"; }
		var socket = io.connect('http://localhost:'+port+'/');
		
		socket.on('welcome', function(data){
			console.log("connected!")
			console.log(JSON.stringify(data));
		});
	
		// responses to action or chat room function
		socket.on('response', function(data){
			console.log(JSON.stringify(data));
		});
	
		// responses to chatRoom
		socket.on('say', function(data){
			console.log(JSON.stringify(data));
		})
	
		// get my details
		var getDetails = function(){
			socket.emit("detailsView");
		}
	
		// call an action
		var action = function(action, params){
			// params = {key1: 'value_1', key2: 'value2'}
			if (params == null){ params = {}; }
			params['action'] = action;
			socket.emit("action", params);
		}
	
		// chat room functions
		var say = function(message){
			// message = "hello world"
			socket.emit("say", {message: message});
		}
		var roomView = function(){
			socket.emit("roomView");
		}
		var roomChange = function(room){
			// room = "newRoomName"
			socket.emit("roomChange", {room: room});
		}
	
		// disconnect
		var quit = function(){
			socket.emit("quit");
		}
	
	</script>

```

Data is always returned as JSON objects.  

An example session in Chrome's console using the above example might be the following:

```javascript

	> connected! webSockets.html:8
	{"welcome":"Hello! Welcome to the actionHero api","room":"defaultRoom","context":"api"} webSockets.html:9
	> getDetails()
	undefined
	{"context":"response","status":"OK","details":{"params":{},"public":{"id":"ACRK5UrC-KNQBZs_-n-d","connectedAt":1352176742663},"room":"defaultRoom"},"messageCount":1} webSockets.html:14
	> action('cacheTest', {key: 'myKey', value: 'myValue'})
	undefined
	{"context":"response","cacheTestResults":{"saveResp":true,"sizeResp":1,"loadResp":{"value":"myValue","expireTimestamp":null,"createdAt":1352176833690,"readAt":1352176833690},"deleteResp":true},"messageCount":2} webSockets.html:14

```