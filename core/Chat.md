## General

All persistent connections (TCP and web socket) are joined to a chat room upon connection.  This room is defined in `api.configData.general.defaultChatRoom`.  Rooms are used to broadcast messages from the system or other users.  Rooms can be created on the fly and don't require any special setup.  In this way. you can push messages to your users with a special functions. 

The special action for persistent connections `say` makes use of `api.chatRoom.socketRoomBroadcast` tell a message to all other users in the room, IE: `say Hello World`.

## Methods

### `api.chatRoom.socketRoomBroadcast(api, connection, message, [fromQueue])`
* tell a message to all members in a room.
* `fromQueue` is an internal optional parameter to indicate if the message has come form a peer connected to this server, or another peer in the actionCluster.
* If you want your API to send messages to all connected clients, you can provide a `null` connection, and `api.configData.general.serverName` will appear as the sender IE `api.chatRoom.socketRoomBroadcast(api, null, "Hello.  The time is " + new Date());`.
* If you don't want to have messages from from the default room (or want to provide more context about the message sender, perhaps the action or server's name), you can mock the connection object: `var mockConnection = {room: "someOtherRoom", public: {id: 0}};`
* The `context` of messages sent with `api.chatRoom.socketRoomBroadcast` always be `user` to differentiate these responses from a `response` to a request
 
### `api.chatRoom.socketRoomStatus(api, room, next)`
* return the status object which contains information about a room and its members


## Chatting with an HTTP(s) client

There are some caveats when chatting with a web client that the other connection methods don't have.  First, there are no chatting primitives, but an example method, [chat.js](https://github.com/evantahler/actionHero/blob/master/actions/chat.js), has been provided.  This action uses the param of `method` to select which chat action to preform.  Examples would be:

- `curl ""localhost:8080/chat/?method=roomView""`
- `curl "localhost:8080/chat/?method=say&message=hello%20world"`
- `curl "localhost:8080/chat/?method=messages"`

Other notes about HTTP(S) chatting

- When a web client connected, actionHero will attempt to set it's ID via cookie.  If a cookie cannot be set, than each request will get a new ID.  If you are expecting http(s) clients which cannot accept cookies, than you can toggle on `api.configData.commonWeb.fingerprintOptions.onlyStaticElements`.  This will use only elements from the connection (headers, etc) which will not change to generate the fingerprint at the expense of entropy.
- Messages sent to http(s) clients can be retrieved with the `api.webServer.getWebChatMessage(api, connection, next)` method
- Messages sent to http(s) clients will not be kept forever (as this would use an ever increasing amount of memory).  How long messages re kept for is defined by `api.configData.commonWeb.httpClientMessageTTL`

### Examples

To demonstrate how to use these methods, [you can craft an action made to be used by HTTP clients to send messages to chat rooms](https://github.com/evantahler/actionHero/blob/master/actions/chat.js) (although they can't receive messages back in-line).