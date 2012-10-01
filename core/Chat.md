## General

All persistent connections (TCP and web socket) are joined to a chat room upon connection.  This room is defined in `api.configData.general.defaultChatRoom`.  Rooms are used to broadcast messages from the system or other users.  Rooms can be created on the fly and don't require any special setup.  In this way. you can push messages to your users with a special functions. 

The special action for persistent connections `say` makes use of `api.chatRoom.socketRoomBroadcast` tell a message to all other users in the room, IE: `say Hello World`.

## Methods

### `api.chatRoom.socketRoomBroadcast(api, connection, message, [fromQueue])`
* tell a message to all members in a room.
* `fromQueue` is an internal optional parameter to indicate if the message has come form a peer connected to this server, or another peer in the actionCluster.
* If you want your API to send messages to all connected clients, you can provide a `null` connection, and `api.configData.general.serverName` will appear as the sender.
* The `context` of messages sent with `api.chatRoom.socketRoomBroadcast` always be `user` to differentiate these responses from a `response` to a request
 
### `api.chatRoom.socketRoomStatus(api, room, next)`
* return the status object which contains information about a room and its members

## Examples

To demonstrate how to use `api.chatRoom.socketRoomBroadcast`, [you can craft an action made to be used by HTTP clients to send messages to chat rooms](https://github.com/evantahler/actionHero/blob/master/actions/say.js) (although they can't receive messages back in-line).

