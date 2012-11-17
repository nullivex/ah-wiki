## General

You can also access actionHero's methods via a persistent socket connection rather than http.  The default port for this type of communication is 5000.  There are a few special actions which set and keep parameters bound to your session (so they don't need to be re-posted).  These special methods are:

* `quit` disconnect from the session
* `paramAdd` - save a singe variable to your connection.  IE: 'addParam screenName=evan'
* `paramView` - returns the details of a single param. IE: 'viewParam screenName'
* `paramDelete` - deletes a single param.  IE: 'deleteParam screenName'
* `paramsView` - returns a JSON object of all the params set to this connection
* `paramsDelete` - deletes all params set to this session
* `roomChange` - change the `room` you are connected to.  By default all socket connections are in the `api.configData.defaultChatRoom` room.   
* `roomView` - show you the room you are connected to, and information about the members currently in that room.
* `detailsView` - show you details about your connection, including your public ID.
* `say` [message]

Please note that any params set using the above method will be 'sticky' to the connection and sent for all subsequent requests.  Be sure to delete or update your params before your next request.

Every socket action will return a single line denoted by `\r\n` which is a JSON object.  If the Action was executed successfully, the response will be `{"status":"OK"}`.

To help sort out the potential stream of messages a socket user may receive, it is best to set a "context" as part of the JSON response.  For example, by default all actions set a context of "response" indicating that the message being sent to the client is response to a request they sent (either an action or a chat action like `say`).  Messages sent by a user via the 'say' command have the context of `user` indicating they came form a user.  Messages resulting from data sent to the api (like an action) will have the `response` context.

Socket Example:

```javascript

	> telnet localhost 5000
	Trying 127.0.0.1...
	Connected to localhost.
	Escape character is '^]'.
	{"welcome":"Hello! Welcome to the actionHero api","room":"defaultRoom","context":"api","messageCount":0}
	detailsView
	{"context":"response","status":"OK","details":{"params":{},"public":{"id":"86b43f5a32e6addb08d7cacd8773325e","connectedAt":1346909099674}},"messageCount":1}
	randomNumber
	{"context":"response","randomNumber":0.6138995781075209,"messageCount":2}
	cacheTest
	{"context":"response","error":"key is a required parameter for this action","messageCount":3}
	paramAdd key=myKey
	{"status":"OK","context":"response","messageCount":4}
	paramAdd value=myValue
	{"status":"OK","context":"response","messageCount":5}
	paramsView
	{"context":"response","params":{"action":"cacheTest","limit":100,"offset":0,"key":"myKey","value":"myValue"},"messageCount":6}
	cacheTest
	{"cacheTestResults":{"key":"myKey","value":"myValue","saveResp":"new record","loadResp":"myValue","deleteResp":true},"messageCount":7}
	say hooray!
	{"context":"response","status":"OK","messageCount":8}
```
	
In your actions, you can send a message directly to a TCP client (without relying on chat rooms) like this:`api.sendSocketMessage(api, connection, message)`

## TLS

You can switch your TCP server to use TLS encryption if you desire.  Just toggle the settings in `config.json` and provide valid certificates.  You can test this with the openSSL client rather than telnet `openssl s_client -connect 127.0.0.1:5000`

	configData.tcpServer = {
		"enable": true,
		"secure": true,
		"port": 5000,
		"bindIP": "0.0.0.0", // which IP to listen on (use 0.0.0.0 for all)
		"keyFile": "./certs/server-key.pem", // only for secure = true
		"certFile": "./certs/server-cert.pem", // only for secure = true
	};

## Files and Routes for TCP clients

Connections over socket can also use the file action.  There is no 'route' for files.

* errors are returned in the normal way `{error: someError}` when they exist.
* a successful file transfer will return the raw file data in a single send().  There will be no headers set, not will the content be JSON.

## JSON Mode 

The default method of using actions for TCP clients is to use the methods above to set params to their session and then call actions inline.  However, you can also communication via JSON, passing along params specific to each request.

- `{"action": "myAction", "params": {"key": "value"}}` is also a valid request over TCP
- params passed within an action's hash will NOT be 'sticky' to the connection, unlike `paramAdd` which remains 'sticky'

## Client Suggestions
The main `trick` to working with TCP/wire connections directly is to remember that you can have many 'pending' requests at the same time.  Also, the order in which you recieve responses back can be variable.  if you request `slowAction` and then `fastAction`, it's fairly likley that you will get a resposne to `fastAction` first.

[The actionHero client library](https://github.com/evantahler/actionhero_client) uses TCP/TLS connections, and makes use of actionHero's `messageCount` paramiter to keep track of requests, and keeps response callbacks for actions in a pending queue.  You can check out the [example here](https://github.com/evantahler/actionhero_client/blob/master/actionhero_client.js)



