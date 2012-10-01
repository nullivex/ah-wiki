## Logging

The `api.log()` method is available to you throughout the application.  `api.log()` will both write these log messages to file, but also display them on the console.  

There are formatting options you can pass to `api.log(yourMessage, options=[])`.  The options array can be many colors and formatting types, IE: `['blue','bold']`.  

For example:

- `api.log("OMG ERROR", ['red','bold])` is a serious error
- `api.log("ActionHero booted", "green")` is a happy boot message.