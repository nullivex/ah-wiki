# Example Actions

## Actions which come bundled with a new actionHero project

**[actionsView](https://github.com/evantahler/actionHero/blob/master/actions/actionsView.js)**: This method shows how to iterate though actionHero's actions and use the metadata to self-describe the capabilities of the API to a client.  This method also crafts responses which describes the special actions for persistent (TCP and webSocket) clients.

**[file](https://github.com/evantahler/actionHero/blob/master/actions/file.js)**: An action which uses actionHero's flat file server to render html content.  In this way, TCP and web socket clients can also request files, and you can request file content as an action.

**[cachetest](https://github.com/evantahler/actionHero/blob/master/actions/cacheTest.js)**: This action demonstrates how to handle parameter checking for an actin, and how to use the internal cache methods of actionHero to save and recall data provided from a client.

**[randomNumber](https://github.com/evantahler/actionHero/blob/master/actions/randomNumber.js)**: This example shows how to craft a simple action with no input, but to respond differently to clients based on their HTTP method (if it exists)

**[say](https://github.com/evantahler/actionHero/blob/master/actions/say.js)**: This action shows how it is possible to use the chatRoom features of actionHero such that a web client can broadcast a message to all persistently connected clients (TCP and webSockets)

**[status](https://github.com/evantahler/actionHero/blob/master/actions/status)**: This action will render some statistics about this actionHero node, and all nodes in the cluster.  Useful for health checks.

## Other example Actions

**[[Generate-Project-Zip]]**: This action will generate a template actionHero project by downloading the needed files from nom, zipping them, and passing them on to the client (web only).  This action demonstrates how to shell out to your OS to preform more complex actions while holding the connection in a non-blocking manner. 