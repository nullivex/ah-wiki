# Example Actions

## Tasks which come bundled with a new actionHero project

**[runAction](https://github.com/evantahler/actionHero/blob/master/tasks/runAction.js)**: This (non periodic) task is used to call an action in the background.  For example, you might have an action to `sendEmail` which can be called synchronously by a client, but you also might want to call it in a delayed manner.  This is the task for you!

## Other example Tasks

**[cleanLogFiles](https://github.com/evantahler/actionHero/blob/master/examples/tasks/cleanLogFiles.js)**: This periodic task will run on all servers and inspect actionHero's `log` directly for large log files, and delete them

**[cleanOldCacheObjects](https://github.com/evantahler/actionHero/blob/master/examples/tasks/cleanOldCacheObjects.js)**: This periodic task will run on one server, and iterate though actionHero's cache looking for data which has expired, and deltas it to save space

**[pingSocketClients](https://github.com/evantahler/actionHero/blob/master/examples/tasks/pingSocketClients.js)**: This periodic task will run on all servers and send a 'ping' to any connected TCP clients to help them keep their 