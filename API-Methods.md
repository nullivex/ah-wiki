# Internal Methods

A collection of actionHero's internal methods which may be useful to others.

## Actions

### new api.actionProcessor(data)

    new api.actionProcessor({
      connection: connection,
      callback: next,
    })

- `next(connection, toContinue)`
- process an action in-line
- connection must be a properly formatted connection object (best to use `api.utils.setupConnection`)

### actionProcessor.processAction(messageId)
- process the action pending `this.commection`
- messageID is optional, and used for clients which can have more than one pending action (TCP, webSocket)

## Connection

### new api.connection(data)

    connection = new api.connection({
        type "web",
        remoteIp: '123.123.123.123',
        remorePort: 80,
        rawConnection: {
          req: req,
          res: res,
        },
    });
- data is required, and must contain: `{type: type, remotePort: remotePort, remoteIP: remoteIP, rawConnection: rawConnection}`
- If your connection already has an ID (from socket.io or browser_fingerprint), you can also pass `data.id`, otherwise a new id will be generated.
- When connections are built, they are added automatically to `api.connections`
- `.connection.params`, `connection.response`, etc are automatically built into the connection object

### connection.destroy()
- The proper way to disconnect a client.  This will remove the connection from any chatrooms and global lists.

### connection.sendMessage(message, type)
* You may use this method to send a message to a specific client which exists in `api.connections.connections`.  
* `sendMessage` abstracts the type of connection so you don't need to use `write` (socket clients) or `publish` (websockets).  
* message is a JSON block which will be serialized and type is optional, and used as the type name of the event to emit (optional, depends on transport)


### api.connections.connections
- the array of all active connections for this server

## Cache

### api.cache.size(next)
- `next(error, count)`
- counts the number of elements in the cache

### api.cache.save(key, value, expireTimeMS, next)
- `next(error, didSave)`
- expireTimeMS can be null if you don't want it to expire
- key must be a string
- value must be able to be JSON.stringify'd


### api.cache.load(key, next) | api.cache.load(key, options, next)
- `next(error, cacheObj.value, cacheObj.expireTimestamp, cacheObj.createdAt, cacheObj.readAt)`
- all callback values will be null if the object doesn't exist or has expired
- options can be `{expireTime: 1234}` where the act of reading the key will reset the key's expire time

### api.cache.destroy(key, next)
- `next(error, didDelete)`
- didDelete will be true if the object existed and was deleted.


## Chat Rooms

### api.chatRoom.socketRoomBroadcast(connection, message)
- connection can be null, and defaults will be assigned
- You can also choose which clients receive messages with `api.chatRoom.socketRoomBroadcast` by configuring `params.roomMatchKey` and `params.roomMatchValue` on the sending client.  This will only broadcast messages to clients (in the same room who match).  Examples include: `&roomMatchKey=id&roomMatchValue=123456` or `&roomMatchKey=auth&roomMatchValue=true`.
- When setting special params on `connection` from within actions, be sure to check for `_originalConnection` (for webSockets and socket clients): `connection.auth = "true";  if(connection._original_connection != null){ connection._original_connection.auth = "true"; }`
 

### api.chatRoom.socketRoomStatus(room, next)
- next(err, roomData)
- roomData = {room: roomName, members: [], membersCount: 3}

### api.chatRoom.roomAddMember(connection, next)
- next(err, wasAdded)

### api.chatRoom.roomRemoveMember(connection, next)
- next(err, wasRemoved)

### api.chatRoom.announceMember(connection, direction)

- direction can be true (member added) or false (member leaving)

## File Server

### api.staticFile.staticFile(file, next)
- next (exists? boolean)

### api.staticFile.get(connection, next)
- next(connection, error, fileStream, mime, length)
- note that fileStream is a stream you can `pipe` to the client, not the file contents

## Redis

### api.redis.client
- the connected redis client
- use this redis instance to make queries, etc


## Faye

#### api.faye.client.subscribe(channel, callback)
- channel is a string of the form "/my/channel"
- callback will be passed `message` which is an object

#### api.faye.client.publish(channel, message);
- channel is a string of the form "/my/channel"
- message is an object


## Socket Server

#### api.socketServer.sendSocketMessage(connection, message)
- message will be JSON.stringify'd


## Stats

### api.stats.increment(key, count, next)
- next(err, wasSet)
- key is a string of the form ("thing:stuff")
- count is a signed integer
- - this method will work on local and global stats

### api.stats.set(key, count, next)
- next(err, wasSet)
- key is a string of the form ("thing:stuff")
- count is a signed integer
- this method will only work on local stats

### api.stats.get(key, collection, next)
- next(err, data)
- key is a string of the form ("thing:stuff")
- collection is either:
  - `api.stats.collections.local`
  - `api.stats.collections.global`

### api.stats.getAll(next)
- next(err, stats)
- stats is a hash of `{global: globalStats, local: localStats}`
- keys will be collapsed into a hash 


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


### api.tasks.getAllTasks(nameToMatch, next)
- next(err, results)
- returns all the details of all enqueued and processing tasks

### api.tasks.queueLength(queue, next)
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

### api.webServer.storeWebChatMessage(connection, messagePayload, next)
- next(null)
- stores a message for a web client

### api.webServer.changeChatRoom(connection, next)
- next(null)
- helper to change the chat room of the virtual connection for a web client

### api.webServer.getWebChatMessage(connection, next)
- next(err, message)
- this will only return one message
- message might be null if there are not message for this client


## Log

### api.log(message, severity, metadata)
- message is a string
- severity is a string, and should match the log-level (IE: 'info' or 'warning')
- the default severity level is 'info'
- (optional) metadata is anything that can be stringified with `JSON.stringify`

api.logger.log and api.logger[severity] also exist


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

### api.utils.hasifyNestedString = function(string, value, hash, seperator)
- used to collapse a string like "thing:stff" and value "val" into an object like `{thing: { stuff: val }}`