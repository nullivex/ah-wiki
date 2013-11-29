## Development Mode

Development mode, when enabled, will poll for changes in your actions, tasks and initializers, and reload them on the fly.

- this uses fs.watchFile() and will not work on all OSs / file systems.
- new files won't be loaded in, only existing files when the app was booted will be monitored
- as deleting a file might crash your application, we will not attempt to re-load deleted files
- if you have changed the `task.frequency` of a periodic task, you will continue to use the old value until the task fires at least once after the change 
- changing `config.js`, initializers, or servers, will attempt to do a "full" reboot the server rather than just reload that component.

#### Don't use this in production!

## Debugging

You can use the awesome [node-inspector](https://github.com/dannycoates/node-inspector) project to help you debug your actionHero application within the familar Chrome Browser's developer tools.


```javascript
"dependencies": {
  "actionHero": "6.1.0",
  "node-inspector": "x"
},
```

Be sure to run actionHero with node's `--debug` flag

```bash
node --debug ./node_modules/.bin/actionHero start
```

Start up node-inspector (both node-inspector and actionHero have the same default port, so you will need to change one of them)

```bash
  ./node_modules/.bin/node-inspector --web-port=1234
```

That's it! Now you can visit `http://0.0.0.0:1234/debug?port=5858` and start debugging.  Remember that the way node-debugger works has you first set a breakpoint in the file view, and then you can use the console to inspect various objects.  IE: I put a breakpoint in the default `status` action in the `run` method:

`api.bootTime`
- 1372739939789

`api.actions.actions['status'][1.0].description`
- "I will return some basic information about the API"