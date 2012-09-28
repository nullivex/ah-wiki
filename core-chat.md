# Chat Rooms

All persistent connections (TCP and web socket) are also joined to a chat room.  Rooms are used to broadcast messages from the system or other users.  Rooms can be created on the fly and don't require any special setup.  In this way. you can push messages to your users with a special function: `api.chatRoom.socketRoomBroadcast(api, connection, message, [fromQueue])`.  `connection` can be null if you want the message to come from the server itself.  The special action for persistent connections is `say` which will tell a message to all other users in the room, IE: `say Hello World`.

API Functions for helping with room communications are below.  You can craft actions to use these methods to also allow http clients to "chat".

* `api.chatRoom.socketRoomBroadcast(api, connection, message, [fromQueue])`: tell a message to all members in a room.  `fromQueue` is an internal optional parameter to indicate if the message has come form a peer connected to this server, or another peer in the actionCluster.
* `api.chatRoom.socketRoomStatus(api, room, next)`: return the status object which contains information about a room and its members