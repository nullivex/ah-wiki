## General

actionHero uses [socket.io](http://socket.io/) for web sockets.  Within actionHero, web sockets are bound to either the web server (either http or https).  Also, if you are using a redis backend store (which is required to use actionHero in a cluster), socket.io will be configured to use this store automatically.

Just like the additional actions added for TCP connection, web socket connections have access to the chat room methods.  A template which exposes them is [provided in actionHero and you can view it here](https://github.com/evantahler/actionHero/blob/master/examples/clients/web/actionHeroWebSocket.js).  You will note that there are methods provided to use the chat methods and acquire metadata.

Data is always returned as JSON objects to the webSocket client.  

An example web socket session might be the following:

```javascript

    <script src="/socket.io/socket.io.js"></script>
    <script src="/public/javascript/actionHeroWebSocket.js"></script>
    
    <script>
      
      s = new actionHeroWebSocket
      s.on('connected', function(){
        console.log('websocket connected');
        s.action("cacheTest", {key: "key", value: "value"}, function(err, response, dela){
           console.log(response);
           s.say('Hey everyone, I passed the test!'); 
        });
    
        s.on('say', function(message){
          console.log(message);
        });    
        
      });
    
      s.on('error', function(message){
        console.log("web socket error: " + message);
      });
    
    </script>

```

Note that we are using the provided actionHeroWebSocket prototype and requiring the socket.io library which actionHero provides.

Methods which the provided actionHeroWebSocket object expose are:

- `actionHeroWebSocket.on('error', function(message){})`
  - string message about the error
- `actionHeroWebSocket.on('welcome', function(message))`
  - welcome message from the server.  
  - Used this to know when you are both connected and able to communicate with the server
- `actionHeroWebSocket.action(action, params, callback)`
  - action: string (like 'login' or 'cacheTest')
  - params: hash of params (like {key: 'a', value: 'b'})
  - callback will be based a response hash from the server
- `actionHeroWebSocket.say(message, callback)`
  -  say: string message to broadcast to the current chatroom
  - callback will be based a response hash from the server
- `actionHeroWebSocket.roomView(callback)`
  - callback will be based a response hash from the server
- `actionHeroWebSocket.roomChange(room, callback)`
  - room: string
  - callback will be based a response hash from the server
- `actionHeroWebSocket.listenToRoom(room, callback)`
  - room: string
  - callback will be based a response hash from the server
- `actionHeroWebSocket.silenceRoom(room, callback)`
  - room: string
  - callback will be based a response hash from the server
- `actionHeroWebSocket.quit()`
  - instantaneous 

You don't need to use the actionHeroWebSocket client, and can connect to the web socket manually and emit `say` and `action` etc commands as you would expect.

You can pass options to socket.io via `config.json`.  There are 2 objects: `settings` and `options`.  settings use the `io.enable` method (key), and options use `io.set` method (key, value).  Here's an example to force only Ajax (XHR) requests which would allow websockets to function on Heroku:
 
```javascript

	/////////////////
	// Web Sockets //
	/////////////////
	
	configData.webSockets = {
	    // You must have the web server enabled as well
	    "enable": true,
	    "logLevel" : 1,
	    "settings" : [
	        "browser client minification",
	        "browser client etag",
	        "browser client gzip"
	    ],
	    "options" : {
	        "transports": ["xhr-polling"],
	        "polling duration": 10
	    }
	};
```