# Servers

In actionHero v6 and later, we have introduced a modular server system.  This allows you to create your own servers or extend the build in ones.  

In actionHero, the goal of each server is to ingest a specific type of connection and transform each client into a generic `connection` object which can be operated on by the rest of actionHero.  To help with this, all servers extend `api.genericServer` and fill in the required methods.

To get started, you can use the `generateServer action` (name is required).  This will generate a template server which looks like this:

```javascript
var test = function(api, options, next){

  //////////
  // INIT //
  //////////

  var type = "test"
  var attributes = {
    canChat: true,
    logConnections: true,
    logExits: true,
    sendWelcomeMessage: true,
    verbs: [],
  }

  var server = new api.genericServer(type, options, attributes);

  //////////////////////
  // REQUIRED METHODS //
  //////////////////////

  server._start = function(next){}

  server._teardown = function(next){}

  server.sendMessage = function(connection, message, messageCount){}

  server.sendFile = function(connection, error, fileStream, mime, length){};

  server.goodbye = function(connection, reason){};

  ////////////
  // EVENTS //
  ////////////

  server.on("connection", function(connection){});

  server.on("actionComplete", function(connection, toRender, messageCount){});

  /////////////
  // HELPERS //
  /////////////

  next(server);
}

/////////////////////////////////////////////////////////////////////
// exports
exports.test = test;

```

Like initializers, the `_start` and `_teardown` methods will be called when the server is to boot up in actionHero's lifecycle, but before any clients are permitted into the system.  Here is where you should actually initialize your server (IE: `https.createServer.listen`, etc).

Your job, as a server designer, is to coerce every client's connection into a connection object.  This is done with the `sever.buildConnection` helper.  Here is an example from the `web` server:

```javascript

server.buildConnection({
  rawConnection: {
    req: req, 
    res: res, 
    method: method, 
    cookies: cookies, 
    responseHeaders: responseHeaders, 
    responseHttpCode: responseHttpCode,
    parsedURL: parsedURL
  }, 
  id: fingerprint, 
  remoteAddress: remoteIP, 
  remotePort: req.connection.remotePort}
); // will emit "connection"

```

Note that connections will have a `rawConnection` property.  This is where you should store the actual object(s) returned by your server so that you can use them to communicate back with the client.  Again, an example from the `web` server:

```javascript
server.sendMessage = function(connection, message){
   cleanHeaders(connection);
   var headers = connection.rawConnection.responseHeaders;
   var responseHttpCode = parseInt(connection.rawConnection.responseHttpCode);
   var stringResponse = String(message)
   connection.rawConnection.res.writeHead(responseHttpCode, headers);
   connection.rawConnection.res.end(stringResponse);
   server.destroyConnection(connection);
 }

```

## Options and Attributes

A server defines `attributes` which define it's behavior.  Variables like `canChat` are defined here.  `options` are passed in, and come from `api.configData.servers[serverName]`.  These can be new variables (like https?) or they can also overwrite the set `attributes`.

The required attributes are provided in a generated server.

## Verbs

When an incoming message is detected, it is the server's job to build `clonnection.params`.  In the `web` server, this is accomplished by reading GET, POST, and form data.  For `websocket` clients, that information is expected to be emitted as part of the action's request.  For other clients, like `socket`, actionHero provides helpers for long-lasting clients to operate on themselves.  These are called connection `verbs`.

Clients use verbs to add params to themselves, update the chat room they are in, and more.   The list of verbs currently supported is:

```
allowedVerbs: [
      "quit", 
      "exit",
      "paramAdd",
      "paramDelete",
      "paramView",
      "paramsView",
      "paramsDelete",
      "roomChange",
      "roomView",
      "listenToRoom",
      "silenceRoom",
      "detailsView",
      "say",
      "setIP",
      "setPort"
    ]
```

Your server should be smart enough to tell when a client is trying to run an action, request a file, or use a verb.  One of the attributes of each server is `allowedVerbs`, which defines what verbs a client is allowed to preform.  A simplified example of how the `socket` server does this:

```javascript
var parseRequest = function(connection, line){
   var words = line.split(" ");
   var verb = words.shift();
   if(verb == "file"){
     if (words.length > 0){
       connection.params.file = words[0];
     }
     server.processFile(connection);
   }else{
     connection.verbs(verb, words, function(error, data){
       if(error == null){
         var message = {status: "OK", context: "response", data: data}
         server.sendMessage(connection, message);
       }else if(error === "verb not found or not allowed"){
         connection.error = null;
         connection.response = {};
         server.processAction(connection);
       }else{
         var message = {status: error, context: "response", data: data}
         server.sendMessage(connection, message);
       }
     });
   }
 }
```

## Chat

The `attribute` "canChat" defines if clients of this server can chat.  If clients can chat, they will be added to `defaultRoom` when they join, and removed from their current room when they leave.  They will also be sent messages in their room (and rooms they are listening too) automatically.