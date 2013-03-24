## General

actionHero uses [faye](http://faye.jcoglan.com/) for web sockets.  Faye provides an abstraction for web sockets which allow fallback to long-polling and other protocols which should be appropriate for the vast majority of clients. Within actionHero, web sockets are bound to the web server (either http or https).  Also, if you are using a redis backend store (which is required to use actionHero in a cluster), faye will be configured to use this store automatically.  actionHero uses the [faye-node-redis](https://github.com/faye/faye-redis-node) backend to ensure that all the nodes in your ActionCluster can serve content for any client (no need for 'sticky' load balancer sessions)

Just like the additional actions added for TCP connection, web socket connections have access to the chat room methods.  A suggested template which exposes them is [provided in actionHero and you can view it here](https://github.com/evantahler/actionHero/blob/master/examples/clients/web/actionHeroWebSocket.js).  You will note that there are methods provided to use the chat methods and acquire metadata.

Data is always returned as JSON objects to the webSocket client.  

An example web socket session might be the following:

```javascript

    <script src="/faye/client.js"></script>
    <script src="/public/javascript/actionHeroWebSocket.js"></script>
    
    <h1>Websocket Example</h1>
    Open the Console for more information.
    
    <script>
    
      var A = new actionHeroWebSocket;
    
      A.events = {
        say: function(message){
          A.log("SAY:");
          A.log(message)
        },
        alert: function(message){
          A.log("ALERT:");
          A.log(message);
        }
      }
    
      A.connect(function(err, details){
        if(err != null){
          A.log(err);
        }else{
          A.log("CONNECTED");
          A.log(details);
        }
      });
    
    </script>

```

Note that we are using the provided actionHeroWebSocket prototype and requiring the faye library which actionHero provides.

Methods which the provided actionHeroWebSocket object expose are:

- `actionHeroWebSocket.prototype.log(message)`
  - formatted logger which uses (console.log)
- `actionHeroWebSocket.prototype.connect(callback)`
- `actionHeroWebSocket.prototype.send(message, callback)`
  - send a raw message
  - you probability don't want to use this
- `actionHeroWebSocket.prototype.setIP()`
  - by default, faye has no notion of the client's IP.  Use this to make an ajax call to set your IP address for the server
- `actionHeroWebSocket.prototype.action(action, params, callback)`
  - `action` is a string, like "login"
  - `params` is an object
  - `callback` will be passed `error`, `response`
- `actionHeroWebSocket.prototype.say = function(message, callback)`
  - `message` is a string
  - `callback` will be passed `error`, `response`
- `actionHeroWebSocket.prototype.detailsView(callback)`
  - `callback` will be passed `error`, `response` 
- `actionHeroWebSocket.prototype.roomView(callback)`
- `actionHeroWebSocket.prototype.roomChange(room, callback)`
  - `room` is a string
  - `callback` will be passed `error`, `response`
- `actionHeroWebSocket.prototype.listenToRoom(room, callback)`
  - `room` is a string
  - `callback` will be passed `error`, `response`
- `actionHeroWebSocket.prototype.silenceRoom(room, callback)`
  - `room` is a string
  - `callback` will be passed `error`, `response`
- `actionHeroWebSocket.prototype.disconnect()`

You don't need to use the actionHeroWebSocket client, and can connect to the web socket manually and emit `say` and `action` etc commands as you would expect.

The websocket server will use settings inherited by the `faye` `config.js` block.  If you want to set options on the client (like specific protocols to use), you can read up on the options [here](http://faye.jcoglan.com/browser.html)
 
```javascript

	/////////////////
	// Web Sockets //
	/////////////////
	
	configData.webSockets = {
	    // You must have the web server enabled as well
	    "enable": true,
	};
```