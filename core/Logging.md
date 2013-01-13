# Logging

The `api.log()` method is available to you throughout the application.  `api.log()` will both write these log messages to file, but also display them on the console.  

There are formatting options you can pass to `api.log(yourMessage, options=[])`.  The options array can be many colors and formatting types, IE: `['blue','bold']`.  

You can disable request logging in your `config.js`

For example:

- `api.log("OMG ERROR", ['red','bold])` is a serious error
- `api.log("ActionHero booted", "green")` is a happy boot message.

There is also a helper to log a complex JSON string: `api.logJSON(hash, color)`. This is often used by actionHero to log connection information: 

    api.logJSON({
      label: "action @ web",
      to: connection.remoteIP,
      action: connection.action,
      request: full_url,
      params: JSON.stringify(connection.params),
      duration: connection.response.serverInformation.requestDuration
    });