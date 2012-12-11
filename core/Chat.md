## General

All persistent connections (TCP and web socket) are joined to a chat room upon connection.  This room is defined in `api.configData.general.defaultChatRoom`.  Rooms are used to broadcast messages from the system or other users.  Rooms can be created on the fly and don't require any special setup.  In this way. you can push messages to your users with a special functions. 

The special action for persistent connections `say` makes use of `api.chatRoom.socketRoomBroadcast` tell a message to all other users in the room, IE: `say Hello World`.

To create private chat rooms (or peer-to-peer rooms), it is suggested that you create a randomly named room and provide each client with an authentication token.

Clients can also subscribe to (but not participate in) chatRooms they are not "in" with `listenToRoom ` and `silenceRoom`

## Methods

### `api.chatRoom.socketRoomBroadcast(api, connection, message, [fromQueue])`
* tell a message to all members in a room.
* `fromQueue` is an internal optional parameter to indicate if the message has come form a peer connected to this server, or another peer in the actionCluster.
* You can provide a `null` connection, and `api.configData.general.serverName` will appear as the sender IE `api.chatRoom.socketRoomBroadcast(api, null, "Hello.  The time is " + new Date());`.  This message will be sent to folks in the default room.
* If you don't want to have messages from from the default room (or want to provide more context about the message sender, perhaps the action or server's name), you can mock the connection object: `var mockConnection = {room: "someOtherRoom", public: {id: 0}};`
* The `context` of messages sent with `api.chatRoom.socketRoomBroadcast` always be `user` to differentiate these responses from a `response` to a request
* There is no limit to the number of chatRooms that can exist, as they are created on the fly as needed.  Your application may want to keep track of which rooms exist explicitly. 

You can also choose which clients recieve messages with `api.chatRoom.socketRoomBroadcast` by configuring `params.roomMatchKey` and `params.roomMatchValue` on the sending client.  This will only broadcast messages to clients (in the same room who match).  Examples include: `&roomMatchKey=id&roomMatchValue=123456` or `&roomMatchKey=auth&roomMatchValue=true`.  

When setting special params on `connection` from within actions, be sure to check for `_originalConnection` (for webSockets and socket clients):

	... // from userLogin.js
	connection.auth = "true";
	if(connection._original_connection != null){
	  connection._original_connection.auth = "true";
	}
	...

 
### `api.chatRoom.socketRoomStatus(api, room, next)`
* next(err, data)
* return the status object which contains information about a room and its members, IE:
	* `{"room":"defaultRoom","members":["ACRK5UrC-KNQBZs_-n-d","cf1cfdce3a287bf5ac6cc71f6dd70a6f"],"membersCount":2}`



## Chatting with an HTTP(s) client

There are some caveats when chatting with a web client that the other connection methods don't have.  First, there are no chatting primitives, but an example method, [chat.js](https://github.com/evantahler/actionHero/blob/master/actions/chat.js), has been provided.  This action uses the param of `method` to select which chat action to preform.  Examples would be:

- `curl ""localhost:8080/chat/?method=roomView""`
- `curl "localhost:8080/chat/?method=say&message=hello%20world"`
- `curl "localhost:8080/chat/?method=messages"`

Other notes about HTTP(S) chatting:

- **We have changed this feature to "opt-in" if you want to use it.**
- `api.configData.commonWeb.httpClientMessageTTL` will default to null (disable http client http(s) message queues).  Setting it an integer (ms) will enable it again.
- When a web client connected, actionHero will attempt to set it's ID via cookie.  If a cookie cannot be set, than each request will get a new ID.  If you are expecting http(s) clients which cannot accept cookies, than you can toggle on `api.configData.commonWeb.fingerprintOptions.onlyStaticElements`.  This will use only elements from the connection (headers, etc) which will not change to generate the fingerprint at the expense of entropy.
- `roomChange` will also store the new room in a cookie.  Cookies are required when changing rooms
- Messages sent to http(s) clients can be retrieved with the `api.webServer.getWebChatMessage(api, connection, next)` method
- Messages sent to http(s) clients will not be kept forever (as this would use an ever increasing amount of memory).  How long messages re kept for is defined by `api.configData.commonWeb.httpClientMessageTTL`

### Examples

To demonstrate how to use these methods, [you can craft an action made to be used by HTTP clients to send messages to chat rooms](https://github.com/evantahler/actionHero/blob/master/actions/chat.js) (although they can't receive messages back in-line).