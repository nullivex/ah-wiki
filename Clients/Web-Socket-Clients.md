## General

actionHero uses [socket.io](http://socket.io/) for web sockets.  Within actionHero, web sockets are bound to either the web server (either http or https).  Also, if you are using a redis backend store (which is required to use actionHero in a cluster), socket.io will be configured to use this store automatically.

Just like the additional actions added for TCP connection, web socket connections have access to the chat room methods.  A template which exposes them is available in examples and looks like this:

```javascript

  <script src="/socket.io/socket.io.js"></script>
  <script>

  var AHsocket = function(connectCallback){
    var self = this;
    var e = new io.EventEmitter();
    for(var i in e){ self[i] = e[i]; }
    if(connectCallback != null){ self.connectCallback = connectCallback;}    
    self.messageCount = 1;
    self.responseTimeout = 30000;
        self.responseHandlers = {};
        self.startingTimeStamps = {};
    self.responseTimesouts = {};
    self.details = {};
    
    self.ws = io.connect('/');

    self.ws.on('error', function(response){
      self.emit('error', "web socket error", response);
    });
  
    self.ws.on('say', function(response){
      self.emit('say', response);
    });
  
    self.ws.on('welcome', function(response){
      self.registerCallback('detailsView', {}, function(err, response, delta){
        self.details = response.details;
        self.emit("connected", self.details);
        if(typeof self.connectCallback == 'function'){
          connectCallback(self.details);
        }
      });
    });
  
    self.ws.on('response', function(response){
      if(typeof self.responseHandlers[response.messageCount] == 'function'){
        var delta = new Date().getTime() - self.startingTimeStamps[response.messageCount];
        self.responseHandlers[response.messageCount](response.error, response, delta);
        delete self.responseTimesouts[response.messageCount];
        delete self.startingTimeStamps[response.messageCount];
        delete self.responseHandlers[response.messageCount];
      }
    });    
  }
  
  AHsocket.prototype.registerCallback = function(event, params, next){
    var self = this;
    if (params == null){ params = {}; }
    if(typeof next == 'function'){
      self.responseHandlers[self.messageCount] = next;
      self.startingTimeStamps[self.messageCount] = new Date().getTime();
      self.responseTimesouts[self.messageCount] = setTimeout(function(){
        self.emit('timeout', event, params, next);
      }, self.responseTimeout, event, params, next);
    }
    self.messageCount++;
    self.ws.emit(event, params);
  }
  
  AHsocket.prototype.action = function(action, params, next){
    if (params == null){ params = {}; }
    params['action'] = action;
    this.registerCallback('action', params, next);
  }
  
  AHsocket.prototype.say = function(message, next){
    this.registerCallback('say', {message: message}, next);
  }

  AHsocket.prototype.roomView = function(next){
    this.registerCallback('roomView', {}, next);
  }

  actionHeroWebSocket.prototype.listenToRoom = function(room, next){
    this.registerCallback('listenToRoom', {room: room}, next);
  }

  actionHeroWebSocket.prototype.silenceRoom = function(room, next){
    this.registerCallback('silenceRoom', {room: room}, next);
  }

  AHsocket.prototype.roomChange = function(room, next){
    this.registerCallback('roomChange', {room: room}, next);
  }

  AHsocket.prototype.quit = function(){
    this.ws.emit("quit");
  }
  
  </script>

```

Data is always returned as JSON objects.  

An example session in Chrome's console using the above example might be the following:

```javascript

  > var s = new AHsocket()
  undefined
  
  > s.details
  Object
    params: Object
    public: Object
      connectedAt: 1352686154534
      id: "X2Y1tyeo8AKl0mTKYHfJ"
        __proto__: Object
      room: "defaultRoom"
        __proto__: Object
  
  > s.action("cacheTest", {key: "key", value: "value"}, function(err, response, dela){
     console.log(response);
  });
  undefined
  Object {context: "response", cacheTestResults: Object, messageCount: 2}

  > s.on('say', function(message){
    console.log(message);
  });
  AHsocket
  
  Object {message: "I have entered the room", room: "defaultRoom", from: "5981c4ccc8347f54d5eec811b76435a2", context: "user", sentAt: 1352686203976}
  Object {message: ""hello there"", room: "defaultRoom", from: "5981c4ccc8347f54d5eec811b76435a2", context: "user", sentAt: 1352686207062}


```

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