# Internal Methods

A collection of actionHero's internal methods which may be useful to others.

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
- You can also choose which clients recieve messages with `api.chatRoom.socketRoomBroadcast` by configuring `params.roomMatchKey` and `params.roomMatchValue` on the sending client.  This will only broadcast messages to clients (in the same room who match).  Examples include: `&roomMatchKey=id&roomMatchValue=123456` or `&roomMatchKey=auth&roomMatchValue=true`.
- When setting special params on `connection` from within actions, be sure to check for `_originalConnection` (for webSockets and socket clients): `connection.auth = "true";  if(connection._original_connection != null){ connection._original_connection.auth = "true"; }`


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
- message will be JSON.stringify'd

## Stats

### api.stats.increment(api, key, count, next)
- next(err, wasSet)
- key is a string
- count is a signed integer
- - this method will work on local and global stats

### api.stats.set(api, key, count, next)
- next(err, wasSet)
- key is a string
- count is a signed integer
- this method will only work on local stats

### api.stats.get(api, key, collection, next)
- next(err, data)
- key is a string
- collection is either:
  - `api.stats.collections.local`
  - `api.stats.collections.global`

### api.stats.getAll(api, next)
- next(err, stats)
- stats is a hash of `{global: globalStats, local: localStats}`


## Tasks

### new api.task()
	var task = new api.task({
		name: "myTaskName",
		runAt: new Date().getTime() + 30000, // run 30 seconds from now
		params: {email: 'evantahler@gmail.com'}, // any optional params to pass to the task
		toAnnounce: true // to log the run of this task or not
	});

### task.enqueue(next)
- run on a task object
- next(err, enqueued)
- adds it to the proper queue

### task.run(next())
- run on a task object
- runs the task in-line, bypassing the task queue(s)


### api.tasks.getAllTasks(api, nameToMatch, next)
- next(err, results)
- returns all the details of all enqueued and processing tasks

### api.tasks.queueLength(api, queue, next)
- next(err, length)
- length is the length of the queue requested
- queues include: 
  - `api.tasks.queues.globalQueue`
  - `api.tasks.queues.delayedQueue`
  - `api.tasks.queues.localQueue`
  - `api.tasks.queues.processingQueue`

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