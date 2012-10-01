
# HTTP(s) client

#### General
You can visit the API in a browser, Curl, etc.  `{url}?action` or `{url}/{action}` is how you would access an action.  For example, using the default ports in `config.js` you could reach the status action with both `http://127.0.0.1:8080/status` or `http://127.0.0.1:8080/?action=status`  The only action which doesn't return the default JSON format would be `file`, as it should return files with the appropriate headers if they are found, and a 404 error if they are not.

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
		},
		error: "OK"
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
	    "error": "OK", 
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

* you can provide the `?callback=myFunc` param to initiate a JSONp response which will wrap the returned JSON in your callback function.  
* unless otherwise provided, the api will set default values of limit and offset to help with paginating long lists of response objects (default: limit=100, offset=0).  These are defined in `config.js`
* the error if everything is OK will be "OK", otherwise, you should set a string error within your action
* to build the response for "hello" above, the action would have set `connection.response.hello = "world";`

You may also enable a HTTPS server with actionHero.  It works exactly the same as the http server, and you can have both running with little overhead.  The following information should be enabled in your `config.js` file:

```javascript

	configData.httpsServer = {
		"enable": true,
		"port": 4443,
		"keyFile": "./certs/server-key.pem",
		"certFile": "./certs/server-cert.pem",
		"bindIP": "0.0.0.0"
	};
```

#### Files and Routes for http and https clients

actionHero can also serve up flat files.  There is an action, `file.js` which is used to do this and a file server is part of the core framework (check out `initFileserver.js` for more information).  actionHero will not cache thees files and each request to `file` will re-read the file from disk (like the nginx web server).

* /public and /api are  routes which expose the 'directories' of those types.  These top level paths can be configured in `config.js` with `api.configData.commonWeb.urlPathForActions` and `api.configData.commonWeb.urlPathForFiles`.
* the root of the web server "/" can be toggled to serve the content between /file or /api actions per your needs `api.configData.commonWeb.rootEndpointType`. The default is `api`.
* actionHero will serve up flat files (html, images, etc) as well from your ./public folder.  This is accomplished via a `file` action or via the 'file' route as described above. `http://{baseUrl}/public/{pathToFile}` is equivalent to `http://{baseUrl}?action=file&fileName={pathToFile}` and `http://{baseUrl}/file/{pathToFile}`. 
* Errors will result in a 404 (file not found) with a message you can customize.
* Proper mime-type headers will be set when possible via the `mime` package.


#### Safe Params

Params provided by the user (GET, POST, etc for http and https servers, setParam for TCP clients, and passed to action calls from a web socket client) will be checked against a whitelist.  Variables defined in your actions by `action.inputs.required` and `action.inputs.optional` will be aded to your whitelist.  Special params which the api will always accept are: 

```javascript

	[
		"callback",
		"action",
		"limit",
		"offset",
		"outputType"
	];
```
	
Params are loaded in this order GET -> POST (normal) -> POST (multipart).  This means that if you have {url}?key=getValue and you post a variable `key`=`postValue` as well, the postValue will be the one used.  The only exception to this is if you use the URL method of defining your action.  You can add arbitrary params to the whitelist by adding them to the `api.postVariables` array in you initializers. 

#### XML

You may also request XML data rather than JSON from actionHero. To do so, you need to pass `outputType=xml` as a param to your request.  