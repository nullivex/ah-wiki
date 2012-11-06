Development mode, when enabled, will poll for changes in your actions and tasks, and reload them on the fly.

- this uses fs.watchFile() and doesn't work on all OSs / file systems.
- new files won't be loaded in, only existing files when the app was booted will be monitored
- as deleting a file might crash your application, we will not attempt to re-load deleted files
- if you have changed the `task.frequency` of a periodic task, you will the old timer value will be in use until the event fires at least once after the change 
- changing `config.js` will attempt to reboot the server

#### Don't use this in production!