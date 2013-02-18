## Example Tasks

**[runAction](https://github.com/evantahler/actionHero/blob/master/tasks/runAction.js)**: This (non periodic) task is used to call an action in the background.  For example, you might have an action to `sendEmail` which can be called synchronously by a client, but you also might want to call it in a delayed manner.  This is the task for you!

**[cleanLogFiles](https://github.com/evantahler/actionHero/blob/master/examples/tasks/cleanLogFiles.js)**: This periodic task will run on all servers and inspect actionHero's `log` directly for large log files, and delete them

**[pingSocketClients](https://github.com/evantahler/actionHero/blob/master/examples/tasks/pingSocketClients.js)**: This periodic task will run on all servers and send a 'ping' to any connected TCP clients to help them keep their 