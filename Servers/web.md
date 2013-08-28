# Web Server

## General

The web server exposes actions and files over http or https.  You can visit the API in a browser, Curl, etc. `{url}?action=actioName` or `{url}/api/{actioName}` is how you would access an action.  For example, using the default ports in `config.js` you could reach the status action with both `http://127.0.0.1:8080/status` or `http://127.0.0.1:8080/?action=status`  

HTTP responses follow the format:

```javascript

	{
		hello: "world"
		serverInformation: {
			serverName: "actionHero API",
			apiVersion: 1,
			requestDuration: 14
		},
		requestorInformation: {
			remoteAddress: "127.0.0.1",
			RequestsRemaining: 989,
			recievedParams: {
				action: "",
				limit: 100,
				offset: 0
			}
		}
	}
```

HTTP Example: 

```javascript

	> curl 'localhost:8080/api/status' -v | python -mjson.tool
	* About to connect() to localhost port 8080 (#0)
	*   Trying 127.0.0.1...
	* connected
	* Connected to localhost (127.0.0.1) port 8080 (#0)
	> GET /api/status HTTP/1.1
	> User-Agent: curl/7.24.0 (x86_64-apple-darwin12.0) libcurl/7.24.0 OpenSSL/0.9.8r zlib/1.2.5
	> Host: localhost:8080
	> Accept: */*
	> 
	< HTTP/1.1 200 OK
	< Content-Type: application/json
	< X-Powered-By: actionHero API
	< Date: Sun, 29 Jul 2012 23:25:53 GMT
	< Connection: keep-alive
	< Transfer-Encoding: chunked
	< 
	{ [data not shown]
	100   741    0   741    0     0   177k      0 --:--:-- --:--:-- --:--:--  361k
	* Connection #0 to host localhost left intact
	* Closing connection #0
	{
	    "requestorInformation": {
	        "recievedParams": {
	            "action": "status", 
	            "limit": 100, 
	            "offset": 0
	        }, 
	        "remoteAddress": "127.0.0.1"
	    }, 
	    "serverInformation": {
	        "apiVersion": "3.0.0", 
	        "currentTime": 1343604353551, 
	        "requestDuration": 1, 
	        "serverName": "actionHero API"
	    }, 
	    "stats": {
	        "cache": {
	            "numberOfObjects": 0
	        }, 
	        "id": "10.0.1.12:8080:4443:5000", 
	        "memoryConsumption": 8421200, 
	        "peers": [
	            "10.0.1.12:8080:4443:5000"
	        ], 
	        "queue": {
	            "queueLength": 0, 
	            "sleepingTasks": []
	        }, 
	        "socketServer": {
	            "numberOfGlobalSocketRequests": 0, 
	            "numberOfLocalActiveSocketClients": 0, 
	            "numberOfLocalSocketRequests": 0
	        }, 
	        "uptimeSeconds": 34.163, 
	        "webServer": {
	            "numberOfGlobalWebRequests": 5, 
	            "numberOfLocalWebRequests": 3
	        }, 
	        "webSocketServer": {
	            "numberOfGlobalWebSocketRequests": 0, 
	            "numberOfLocalActiveWebSocketClients": 0
	        }
	    }
	}
```

* you can provide the `?callback=myFunc` param to initiate a JSONp response which will wrap the returned JSON in your callback function.  The mime type of the response will change from JSON to Javascript. 
* unless otherwise provided, the api will set default values of limit and offset to help with paginating long lists of response objects (default: limit=100, offset=0).  These defaults are defined in `config.js`
* If everything went OK with your request, no error attribute will be set on the response, otherwise, you should set a string error within your action
* to build the response for "hello" above, the action would have set `connection.response.hello = "world";`

You may also enable a HTTPS server with actionHero.  It works exactly the same as the http server. You cannot have the http and https server running at the same time. The following information should be enabled in your `config.js` file:

```javascript
configData.severs.web = {
  secure: false,                       // HTTP or HTTPS?
  serverOptions: {},                   // passed to https.createServer if secure=ture. Should contain SSL certificates
  port: 8080,                          // Port or Socket
  bindIP: "0.0.0.0",                   // which IP to listen on (use 0.0.0.0 for all)
  httpHeaders : { },                   // Any additional headers you want actionHero to respond with
  urlPathForActions : "api",           // route which actions will be served from; secondary route against this route will be treated as actions, IE: /api/?action=test == /api/test/
  urlPathForFiles : "public",          // route which static files will be served from; path (relitive to your project root) to server static content from
  rootEndpointType : "api",            // when visiting the root URL, should visitors see "api" or "file"? visitors can always visit /api and /public as normal
  directoryFileType : "index.html",    // the default filetype to server when a user requests a directory
  flatFileCacheDuration : 60,          // the header which will be returend for all flat file served from /public; defiend in seconds
  fingerprintOptions : {               // settings for determining the id of an http(s) requset (browser-fingerprint)
    cookieKey: "sessionID",
    toSetCookie: true,
    onlyStaticElements: false
  },
  formOptions: {                       // options to be applied to incomming file uplaods.  more options and details at https://github.com/felixge/node-formidable
    uploadDir: "/tmp",
    keepExtensions: false,
    maxFieldsSize: 1024 * 1024 * 100
  },
  returnErrorCodes: false              // when enabled, returnErrorCodes will modify the response header for http(s) clients if connection.error is not null.  You can also set connection.responseHttpCode to specify a code per request.
};
```

Note that if you wish to create a secure (https) server, you will be required to complete the serverOptions hash with at least a cert and a keyfile:

```javascript
configData.server.web.serverOptions: {
  key: fs.readFileSync('certs/server-key.pem'),
  cert: fs.readFileSync('certs/server-cert.pem')
}
```
	
## The `connection` object

when inspecting `connection` in actions from web client, a few additional elements are added for convenience:

- `connection.rawConnection.responseHeaders`: array of headers which can be built up in the action.  Headers will be made unique, and latest header will be used (except setting cookies)
- `connection.rawConnection.method`: A string, GET, POST, etc
- `connection.rawConnection.cookies`: Hash representation of the connection's cookies
- `connection.rawConnection.responseHttpCode`: the status code to be rendered to the user.  Defaults to 200
- `connection.type` for a HTTP client is "web"

## Files

actionHero can also serve up flat files.  actionHero will not cache these files and each request to `file` will re-read the file from disk (like the nginx web server).

* /public and /api are  routes which expose the 'directories' of those types.  These top level path can be configured in `config.js` with `api.configData.servers.web.urlPathForActions` and `api.configData.servers.web.urlPathForFiles`.
* the root of the web server "/" can be toggled to serve the content between /file or /api actions per your needs `api.configData.servers.web.rootEndpointType`. The default is `api`.
* actionHero will serve up flat files (html, images, etc) as well from your ./public folder.  This is accomplished via the 'file' route as described above. `http://{baseUrl}/public/{pathToFile}` is equivalent to `http://{baseUrl}?action=file&fileName={pathToFile}` and `http://{baseUrl}/file/{pathToFile}`. 
* Errors will result in a 404 (file not found) with a message you can customize.
* Proper mime-type headers will be set when possible via the `mime` package.

## Routes

Web clients (http and https) you can define an optional RESTful mapping to help route requests to actions.  If the client doesn't specify an action via a param, and the base route isn't a named action, the action will attempt to be discerned from this `routes.js` file.

- actions defined in params directly `action=theAction` or hitting the named URL for an action `/api/theAction` will never override RESTful routing 
- you can mix explicitly defined params with route-defined params.  If there is an overlap, the route-defined params win
  - IE: /api/user/123?userId=456 => `connection.userId = 123`
- routes defined with the "all" method will be duplicated to "get", "put", "post", and "delete"
- use ":variable" to defined "variable"
- undefined ":variable" will match
  - IE: "/api/user/" WILL match "/api/user/:userId"
- routes are matched as defined here top-down
- you can optionally define a regex match along with your route variable
  - IE: `{ path:"/game/:id(^[a-z]{0,10}$)", action: "gamehandler" }`
  - be sure to double-escape when needed: `{ path: "/login/:userID(^\\d{3}$)", action: "login" }`

**example**:

```javascript
{
  get: [
    { path: "/users", action: "usersList" }, // (GET) /api/users
    { path: "/search/:term/limit/:limit/offset/:offset", action: "search" }, // (GET) /api/search/car/limit/10/offset/100
  ],

  post: [
    { path: "/login/:userID(^\\d{3}$)", action: "login" } // (POST) /api/login/123
  ],

  all: [
    { path: "/user/:userID", action: "user" } // (*) / /api/user/123
  ]
}
```

## Safe Params

Params provided by the user (GET, POST, etc for http and https servers, setParam for TCP clients, and passed to action calls from a web socket client) will be checked against a whitelist.  Variables defined in your actions by `action.inputs.required` and `action.inputs.optional` will be aded to your whitelist.  Special params which the api will always accept are: 

```javascript
	[
      "file",
      "callback",
      "action",
      "limit",
      "offset",
      "outputType",
      "roomMatchKey",
      "roomMatchValue"
    ]
```
	
Params are loaded in this order GET -> POST (normal) -> POST (multipart).  This means that if you have {url}?key=getValue and you post a variable `key`=`postValue` as well, the postValue will be the one used.  The only exception to this is if you use the URL method of defining your action.  You can add arbitrary params to the whitelist by adding them to the `api.postVariables` array in you initializers. 

File uploads from forms will also appear in `connection.params`, but will be an object with more information.  IE: if you uploaded "image" from a form, you would have `connection.params.image.path`, `connection.params.image.name` (original file name), and `connection.params.image.type` available to you.

## XML

You may also request XML data rather than JSON from actionHero. To do so, you need to pass `outputType=xml` as a param to your request.  

## Uploading Files

actionHero uses the [formidable](https://github.com/felixge/node-formidable) form parsing library.  You can set options for it via `api.configData.commonWeb.formOptions`.  You can upload multiple files to an action and they will be available within `connection.params` as formidable response objects containing references to the original file name, where the uploaded file was stored temporarily, etc.   Here's an example:

#### uploader.js (action)

```javascript
exports.action = {
  name: 'uploader',
  description: 'uploader',
  inputs: {
    required: [], optional: ['file1', 'file2', 'key1', 'key2']
  }, 
  outputExample: null,
  run: function(api, connection, next){
    console.log("\r\n\r\n")
    console.log(connection.params);
    console.log("\r\n\r\n")
    next(connection, true);
  }
};
```

#### uploader.html (public)

```html
<html>
    <head></head>
    <body>
        <form method="post" enctype="multipart/form-data" action="http://localhost:8080/api/uploader">
            <input type="file" name="file1" />
            <input type="file" name="file2" />
            <br><br>
            <input type='text' name="key1" />
            <input type='text' name="key2" />
            <br><br>
            <input type="submit" value="send" />
        </form>
    </body>
</html>
```

.. and an example `connections.params` with 2 uploaded files and string values:

```
{ action: 'uploader',
  file1: 
   { domain: null,
     _events: null,
     _maxListeners: 10,
     size: 5477608,
     path: '/Users/evantahler/PROJECTS/actionHero/tmp/86b2aa018a9785e20b3f6cea95babcca',
     name: '1-02 Concentration Enhancing Menu Initialiser.mp3',
     type: 'audio/mp3',
     hash: false,
     lastModifiedDate: Wed Feb 13 2013 20:32:49 GMT-0800 (PST),
     _writeStream: 
      { ... },
     length: [Getter],
     filename: [Getter],
     mime: [Getter] },
  file2: 
   { domain: null,
     _events: null,
     _maxListeners: 10,
     size: 10439802,
     path: '/Users/evantahler/PROJECTS/actionHero/tmp/6052010f1d75ceaeb9197a9a759124dc',
     name: '1-10 There She Is.mp3',
     type: 'audio/mp3',
     hash: false,
     lastModifiedDate: Wed Feb 13 2013 20:32:49 GMT-0800 (PST),
     _writeStream: 
      { ... },
  key1: '123',
  key2: '456',
  limit: 100,
  offset: 0 }
```