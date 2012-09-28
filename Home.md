# Welcome to the actionHero wiki!

<img src="https://raw.github.com/evantahler/actionHero/master/public/logo/actionHero.png" height="400"/>

External Links: [NPM](https://npmjs.org/package/actionHero) | [Wiki](https://github.com/evantahler/actionHero/wiki) | [Public Site](http://www.actionherojs.com) | [GitHub](https://github.com/evantahler/actionHero) | [Client](https://github.com/evantahler/actionhero_client)]

---

## Who is the actionHero?
actionHero is a [node.js](http://nodejs.org) **API framework** for both **tcp sockets**, **web sockets**, and **http clients**.  The goal of actionHero are to create an easy-to-use toolkit for making **reusable** & **scalable** APIs.  clients connected to an actionHero server can **consume the api**, **be servers static content**, and **communicate with each other**.

actionHero servers can process both requests and tasks (delayed actions like `send e-mail` or other background jobs).  actionHero servers can also run in a cluster (on the same or multiple machines) to work in concert to handle your load.

The actionHero API defines a single access point and accepts GET, POST, PUT and DELETE input along with persistent connection via TCP or web sockets. You define **Actions** which handle input and response, such as "userAdd" or "geoLocate". HTTP, HTTPS, and TCP clients can all use these actions.  The actionHero API is not inherently "RESTful" (which is meaningless for persistent socket connections) but can be extended to be so if you wish.

actionHero will also serve static files for you, but actionHero is not meant to be a 'rendering' server (like express or rails).

## General
- [Getting Started](core-getting-started)
- [Readme](https://github.com/evantahler/actionHero/blob/master/readme.md)
- [Version History](https://github.com/evantahler/actionHero/blob/master/versions.md)

## Core actionHero Components
- [Actions](https://github.com/evantahler/actionHero/wiki/core-getting-started)
- [Tasks](https://github.com/evantahler/actionHero/wiki/core-tasks)
- [Cache](https://github.com/evantahler/actionHero/wiki/core-cache)
- [Redis](https://github.com/evantahler/actionHero/wiki/core-redis)
- [Cluster](https://github.com/evantahler/actionHero/wiki/core-cluster)
- [config](https://github.com/evantahler/actionHero/wiki/core-config)
- [Exceptions](https://github.com/evantahler/actionHero/wiki/core-exceptions)
- [Logging](https://github.com/evantahler/actionHero/wiki/core-logging)
- [Chat Rooms & connection to connection communication](https://github.com/evantahler/actionHero/wiki/core-chat)
- [Flat File server](https://github.com/evantahler/actionHero/wiki/core-file-server)
- [Extending actionHero](https://github.com/evantahler/actionHero/wiki/core-extending-actionHero)

## Client Types
- [Web](https://github.com/evantahler/actionHero/wiki/client-web)
- [TCP](https://github.com/evantahler/actionHero/wiki/client-tcp)
- [WebSockets](https://github.com/evantahler/actionHero/wiki/client-web-socket)

## Internal Method Documentation
- Internal Methods

## Templates and Examples
- [[Example Actions]]
- [[Example Tasks]]
- [[Custom Initializers]]
- RESTful routing
- actionCluster Servers [[1](https://github.com/evantahler/actionHero/blob/master/examples/servers/actionHero_cluster_peer_1.js)] [[2](https://github.com/evantahler/actionHero/blob/master/examples/servers/actionHero_cluster_peer_2.js)] [[3](https://github.com/evantahler/actionHero/blob/master/examples/servers/actionHero_cluster_peer_3.js)]
- [Using node.js clusters to manage a node](https://github.com/evantahler/actionHero/blob/master/scripts/actionHeroCluster)