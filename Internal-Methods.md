# Internal Methods

A collection of actionHero's internal methods which may be useful to others.

[[_TOC_]]

## Actions

### api.processAction(api, connection, messageID, next)
- `next(connection, toContinue)`
- process an action in-line
- connection must be a properly formatted connection object (best to use `api.utils.setupConnection`)


## Cache

### api.cache.size(api, next)
- `next(error, count)`
- counts the number of elements in the cache

### api.cache.save(api, key, value, expireTimeMS, next)
- `next(error, didSave)`
- expireTimeMS can be null if you don't want it to expire
- key must be a string
- value must be able to be JSON.stringify'd


### api.cache.load(api, key, next)
- `next(error, cacheObj.value, cacheObj.expireTimestamp, cacheObj.createdAt, cacheObj.readAt)`
- all callback values will be null if the object doesn't exist or has expired

### api.cache.destroy(api, key, next)
- `next(error, didDelete)`
- didDelete will be true if the object existed and was deleted.


## Chat Rooms

### api.chatRoom.socketRoomBroadcast(api, connection, message, fromQueue)
- fromQueue is to denote if the message originated from this server or another peer (don't use)
- connection can be null, and defaults will be assigned

### api.chatRoom.socketRoomStatus(api, room, next)
- next(err, roomData)
- roomData = {room: roomName, members: [], membersCount: 3}

### api.chatRoom.roomAddMember(api, connection, next)
- next(err, wasAdded)

### api.chatRoom.roomRemoveMember(api, connection, next)
- next(err, wasRemoved)

### api.chatRoom.announceMember(api, connection, direction)

- direction can be true (member added) or false (member leaving)

## File Server

### api.fileServer.sendFile(api, file, connection, next)
- next(connection, false)
- file is the full path to the file to send

### api.fileServer.sendFileNotFound(api, connection, next)
- next(connection, true)


## Redis

### api.redis.client
- the connected redis client
- use this redis instance to make queries, etc

### api.redis.registerChannel(api, channel, handler)
- subscribe to redis pub/sub messages
- channel is a string
- handler is the callback to be fired on messages
- handler will be send next(channel, message)

### api.redis.client.publish(channel, message);
- channel and message should be strings


## Socket Server

#### api.socketServer.sendSocketMessage(connection, message)
- message will be JSON.stringified

## Stats

### api.stats.increment(api, key, count, next)
- next(err, wasSet)
- key is a string
- count is a signed integer

### api.stats.get(api, key, next)
- next(err, data)
- key is a string

### api.stats.load(api, next)
- next(err, fullStatsData)
- i get a predefined collection of stats objects


## Tasks

### api.tasks.enqueue(api, taskName, runAtTime, params, next, toAnnounce)
- next(err, wasEnqueued)
- taskName is  string
- runAtTime can be null for now
- params is a 1-dimensional hash of data to be passed to the task
- toAnnounce denotes if the task queuing should be logged

### api.tasks.inspect(api, next)
- next(err, tasksArray)
- tasksArray shows all tasks in the global, local, and workingOn queues

### api.tasks.queueLength(api, queue, next)
- next(err, length)
- length is the length of the global queue

### api.tasks.getNextTask(api, next, queue)
- next(err, nextTask)
- nextTask might null if there are no tasks in the queues (both global and local)
- queue can be null, and the global queue will be assumed
- nextTask looks like: `{ taskName: taskName, runAtTime: runAtTime, params: params }`
- this will remove this task from the queue, so be sure to put it back if you don't run it


## WebServer

### api.webServer.respondToWebClient(connection, toRender)
- this renders the connection.response to the client

### api.webServer.storeWebChatMessage(api, connection, messagePayload, next)
- next(null)
- stores a message for a web client

### api.webServer.changeChatRoom(api, connection, next)
- next(null)
- helper to change the chat room of the virtual connection for a web client

### api.webServer.getWebChatMessage(api, connection, next)
- next(err, message)
- this will only return one message
- message might be null if there are not message for this client


## Log

### api.log(original_message, styles)
- original_message is a string
- styles is an array of styles like ["bold", "red"] or just one style as a string "green"

## Utils

### api.utils.sqlDateTime(time)
- returns a mySQL-style formatted time string `2012-01-01 00:00:00`
- if no time is provided, `new Date()` (now) will be used

### api.utils.sqlDate(time)
- returns a mySQL-style formatted date string `2012-01-01`
- if no time is provided, `new Date()` (now) will be used

### api.utils.randomString(bits)
- returns a random string of `bits` entropy
- the chars used are all HTML safe!

### api.utils.hashLength(obj)
- calculates the 'size' of primary, top level keys in the hash
- `api.utils.hashLength({a: 1, b: 2})` would be 2

### api.utils.objClone(obj)
- creates a new object with the same keys and values of the original object

### api.utils.arrayUniqueify(arr)
- removes duplicate entries from an array

### api.utils.randomArraySort(arr)
- return a randomly sorted array

### api.utils.inArray(haystack, needle)
- checks if needle is contained within the haystack array
- returns true/false

### api.utils.sleepSync(seconds)
- a terrible an inneffiencet way to force a blocking sleep in the main thread
- never ever use this… ever

### api.utils.getExternalIPAddress()
- attempts to determine this server's external IP address out of all plausible addressees this host is listening on

### api.utils.parseCookies(req)
- a helper to parse the request object's headers and returns a hash of the client's cookies

### api.utils.setupConnection(api, connection, type, remotePort, remoteIP)
- create a new connection object appropriate for actions, or modify an existing one to add needed fields
- connection can be null or any object
- type should be 'web', 'tcp', etc
- the connection, if new, will be added to chat rooms

### api.utils.destroyConnection(api, connection, next)
- remove all tendrils of a connection
- will be removed from any chat rooms
- will be removed from `api.connections`