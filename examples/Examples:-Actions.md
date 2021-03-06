## Example Actions

**[cachetest](https://github.com/evantahler/actionHero/blob/master/actions/cacheTest.js)**: This action demonstrates how to handle parameter checking for an actin, and how to use the internal cache methods of actionHero to save and recall data provided from a client.

**[randomNumber](https://github.com/evantahler/actionHero/blob/master/actions/randomNumber.js)**: This example shows how to craft a simple action with no input, but to respond differently to clients based on their HTTP method (if it exists)

**[chat](https://github.com/evantahler/actionHero/blob/master/actions/chat.js)**: This action shows how it is possible to use the chatRoom features of actionHero such that a web client can broadcast a message to all persistently connected clients, and use the other chatRoom methods

**[status](https://github.com/evantahler/actionHero/blob/master/actions/status.js)**: This action will render some statistics about this actionHero node, and all nodes in the cluster.  Useful for health checks.

**[inspectTasks](https://gist.github.com/4399793)**: This action will retrieve the status of the task queue.

**[fileResponder](https://gist.github.com/connanp/6169574)**: This snippet is for rendering Jade templates on the server for pushState single page apps in actionHero. Useful when you need to bootstrap model data on initial page load. It also pre-compiles the templates on server start and will recompile if they change.

**[oauth](https://gist.github.com/4326070)**: This file is actually 2 actions which are needed to authenticate a user against twitter's API.  Note the use of `api.cache` to save and load the temporary secret tokens, and how to send custom (redirect) headers with actionHero

**[A database-backed user system](http://blog.evantahler.com/blog/authentication-with-actionHero.html)**: This example contains a working user system (auth, login, user creation, sessions, etc) using a mySQL backend and the sequelize ORM. 

***[Tic-Tac-Toe API](https://gist.github.com/evantahler/5898472)*** This advanced example demonstrates how to create actions (and initializers) which will enable you to play tic-tac-toe against an AI Player.