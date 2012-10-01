## Linking URL routes to params

You can extract prams per HTTP(s) request from the route requested by the user via an included utility.
URL routes remain not being a source of RESTful parameters by default, however, you can opt to parse them: 

```
	var urlMap = ['userID', 'gameID'];
	connection.params = api.utils.mapParamsFromURL(connection, urlMap);
```

- this is still left up to the action as the choice of which to choose as the default: query params, post params, or RESTful params is a deeply personal mater.
- if your connection is TCP or webSockets, `api.utils.mapParamsFromURL` will return null
- map is an array of the param's keys in order (ie: `/:action/:userID/:email/:gameID` => `['userID', 'email', 'gameID']`)
- the action itself will be omitted from consideration in the mapping
- these are equivalent: [ `localhost:8080/a/path/and/stuff?action=randomNumber` ] && [ `localhost:8080/randomNumber/a/path/and/stuff` ]