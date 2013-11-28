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
- If your connection already has an ID (from faye, your server implementation, or browser_fingerprint), you can also pass `data.id`, otherwise a new id will be generated.
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

### api.stats.increment(key, count)
- key is a string of the form ("thing:stuff")
- count is a signed integer

### api.stats.get(key, collection, next)
- next(err, data)
- key is a string of the form ("thing:stuff")
- collection (optional) is one of the collections used for stats set in `api.configData.stats.keys`

### api.stats.getAll(collections, next)
- next(err, stats)
- collections (optional) is an array of one or more of the keys used for stats set in `api.configData.stats.keys`
- stats is a hash of `{key1: stats {}, key2: stats {} }`
- keys will be collapsed into a hash 


## Tasks

### api.tasks.enqueue(taskName, params, queue, next)
- next(err, toRun)
- enqueues a task to run ASAP
- toRun will indicate if the task was successfully enqueued or blocked by a resque plugin

### api.tasks.enqueueAt(timestamp, taskName, params, queue, next)
- next()
- enqueue a task to run at `timestamp`(ms)

### api.tasks.enqueueIn(time, taskName, params, queue, next)
- next()
- enqueue a task to run in `time`ms from now

### api.tasks.del(queue, taskName, args, count, next)
- next(err, count)
- removes all matching instances of queue + taskName + args from the normal queues
- count is how many instances of this task were removed

### api.tasks.delDelayed(queue, taskName, args, next)
- next(err, timestamps)
- removes all matching instances of queue + taskName + args from the delayed queues
- timestamps will be an array of the delayed timestamps which the task was removed from

### api.tasks.enqueueRecurrentJob(taskName, next)
- next()
- will enqueue are recurring job
- might not actually enqueue the job if it is already enqueued due to resque plugins

### api.tasks.stopRecurrentJob(taskName, next)
- next(err, removedCount)
- will remove all instances of `taskName` from the delayed queues and normal queues
- removedCount will inform you of how many instances of this job were removed

### api.tasks.details(next)
- next(err, details)
- details is a hash of all the queues in the system and how long they are

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