# WebSocket Server

## General

actionHero uses [faye](http://faye.jcoglan.com/) for web sockets.  Faye provides an abstraction for web sockets which allow fallback to long-polling and other protocols which should be appropriate for the vast majority of browsers. Within actionHero, web sockets are bound to the web server (either http or https).  Also, if you are using a redis backend store (which is required to use actionHero in a cluster), faye will be configured to use this store automatically.  actionHero uses the [faye-node-redis](https://github.com/faye/faye-redis-node) backend to ensure that all the nodes in your cluster can serve content for any client (no need for 'sticky' load balancer sessions)

### Why not Socket.IO :(

Originally, **actionHero** did use Socket.IO however, Socket.IO was failing us when used in a cluster. See #128

Faye is lighter and more graceful with failures.

Faye still maintains fallbacks to other protocols like long polling.

### Connection Details

As this is a persistent connection, websocket connections have actionHero's verbs available to them.  These verbs are:

* `quit` disconnect from the session
* `roomChange` - change the `room` you are connected to.  By default all socket connections are in the `api.configData.defaultChatRoom` room.   
* `roomView` - show you the room you are connected to, and information about the members currently in that room.
* `listenToRoom` - opt into hearing messages from another chat room
* `silenceRoom` - stop hearing messages from other chat rooms
* `detailsView` - show you details about your connection, including your public ID.
* `say` [message]

`connection.type` for a webSocket client is "webSocket".  This type will not change regardless of if the client has fallen back to another protocol. 

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

The whole list of events you can register for are:

```javascript
A.events = {

disconnect: function(){
  A.log("DISCONNECTED");
},
reconnect: function(){
  A.log("RECONNECTED");
  A.log("New ID: " + A.id);
},
say: function(message){
  A.log("SAY:");
  A.log(message)
},
api: function(message){
  A.log("API:");
  A.log(message)
},
welcome: function(message){
  A.log("WELCOME:");
  A.log(message);
},
alert: function(message){
  A.log("ALERT:");
  A.log(message);
}

}
```

You can also inspect `A.state` ('connected', 'disconnected', etc)

Note that we are using **both** the provided actionHeroWebSocket prototype and requiring the faye library which actionHero provides.

Methods which the provided actionHeroWebSocket object expose are:

- `actionHeroWebSocket.prototype.log(message)`
  - formatted logger which uses (console.log)
- `actionHeroWebSocket.prototype.connect(callback)`
- `actionHeroWebSocket.prototype.send(message, callback)`
  - send a raw message
  - you probability don't want to use this
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

The websocket server will use settings inherited by the `faye` `config.js` block.  If you want to set options on the client (like specific protocols to use), you can read up on the options [here](http://faye.jcoglan.com/browser.html).  Note that changes to server options may require updates to the client library `actionHeroWebsocket.js` as well.