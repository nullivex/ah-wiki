# Chat

## General

All persistent connections (socket and websocket) are joined to a chat room upon connection.  This room is defined in `api.configData.general.defaultChatRoom`.  Rooms are used to broadcast messages from the system or other users.  Rooms can be created on the fly and don't require any special setup.  In this way. you can push messages to your users with a special functions. 

The special action for persistent connections `say` makes use of `api.chatRoom.socketRoomBroadcast` tell a message to all other users in the room, IE: `say Hello World` from a socket clinet.

To create private chat rooms (or peer-to-peer rooms), it is suggested that you create a randomly named room and provide each client with an authentication token.

Clients can also subscribe to (but not participate in) chatRooms they are not "in" with `listenToRoom ` and `silenceRoom`

## Methods

### `api.chatRoom.socketRoomBroadcast(connection, message)`
* tell a message to all members in a room.
* `fromQueue` is an internal optional parameter to indicate if the message has come form a peer connected to this server, or another peer in the actionCluster.
* You can provide a `null` connection, and `api.configData.general.serverName` will appear as the sender IE `api.chatRoom.socketRoomBroadcast(null, "Hello.  The time is " + new Date());`.  This message will be sent to folks in the default room.
* If you don't want to have messages from from the default room (or want to provide more context about the message sender, perhaps the action or server's name), you can mock the connection object: `var mockConnection = {room: "someOtherRoom"};`
* The `context` of messages sent with `api.chatRoom.socketRoomBroadcast` always be `user` to differentiate these responses from a `response` to a request
* There is no limit to the number of chatRooms that can exist, as they are created on the fly as needed.  Your application may want to keep track of which rooms exist explicitly. 

### Specific Clients

You can also choose which clients receive messages with `api.chatRoom.socketRoomBroadcast` by configuring `params.roomMatchKey` and `params.roomMatchValue` on the sending client.  This will only broadcast messages to clients (in the same room who match).  Examples include: `&roomMatchKey=id&roomMatchValue=123456` or `&roomMatchKey=auth&roomMatchValue=true`.  For example, you can send a message to a single connection by ID:

```javascript
var mockConnection = {room: 'lobby', roomMatchKey: 'id', roomMatchValue: 123};
api.chatRoom.socketRoomBroadcast(mockConnection,"Hello user 123");
```

or you can send a message to all connections on `team_a`

```javascript
var mockConnection = {room: 'battlefield', roomMatchKey: 'team', roomMatchValue: 'team_a'};
api.chatRoom.socketRoomBroadcast(mockConnection, "Hello members of Team A");
```

When setting special params on `connection` from within actions, be sure to check for `_originalConnection` (for webSockets and socket clients):

	... // from userLogin.js
	connection.auth = "true";
	if(connection._original_connection != null){
	  connection._original_connection.auth = "true";
	}
	...


### `connection.sendMessage(message, type)`
* You may use this method to send a message to a specific client which exists in `api.connections.connections`.  
* `sendMessage` abstracts the type of connection so you don't need to use `write` (socket clients) or `emit` (websockets).  
* message is a JSON block which will be serialized and type is optional, and used as the type name of the event to emit (if relevent)
 
### `api.chatRoom.socketRoomStatus(room, next)`
* next(err, data)
* return the status object which contains information about a room and its members, IE:
	* `{"room":"defaultRoom","members":["ACRK5UrC-KNQBZs_-n-d","cf1cfdce3a287bf5ac6cc71f6dd70a6f"],"membersCount":2}`