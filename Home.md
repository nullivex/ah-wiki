# Welcome to the actionHero wiki!

**Links: [NPM](https://npmjs.org/package/actionHero) | [Wiki](https://github.com/evantahler/actionHero/wiki) | [Public Site](http://www.actionherojs.com) | [GitHub](https://github.com/evantahler/actionHero) | [Client](https://github.com/evantahler/actionhero_client)**

---

## Who is the actionHero?
actionHero is a [node.js](http://nodejs.org) **API framework** for both **tcp sockets**, **web sockets**, and **http clients**.  The goal of actionHero are to create an easy-to-use toolkit for making **reusable** & **scalable** APIs.  clients connected to an actionHero server can **consume the api**, **consume static content**, and **communicate with each other**.

actionHero servers can process both requests and tasks (delayed actions like `send e-mail` or other background jobs).  actionHero servers can also run in a cluster (on the same or multiple machines) to work in concert to handle your load.

The actionHero API defines a single access point and accepts GET, POST, PUT and DELETE input along with persistent connection via TCP or web sockets. You define **Actions** which handle input and response, such as "userAdd" or "geoLocate". HTTP, HTTPS, and TCP clients can all use these actions.  The actionHero API is not inherently "RESTful" (which is meaningless for persistent socket connections) but can be extended to be so if you wish.

actionHero will also serve static files for you, but actionHero is not meant to be a 'rendering' server (like express or rails).

## General
- [Getting Started](https://github.com/evantahler/actionHero/wiki/Getting-Started)
- [Readme](https://github.com/evantahler/actionHero/blob/master/readme.md)
- [Version History](https://github.com/evantahler/actionHero/blob/master/versions.md)
- [Logo](https://raw.github.com/evantahler/actionHero/master/public/logo/actionHero.png)

## Core actionHero Components
- [Actions](https://github.com/evantahler/actionHero/wiki/Actions)
- [Tasks](https://github.com/evantahler/actionHero/wiki/Tasks)
- [Cache](https://github.com/evantahler/actionHero/wiki/Cache)
- [Redis](https://github.com/evantahler/actionHero/wiki/Redis)
- [Cluster](https://github.com/evantahler/actionHero/wiki/actionCluster)
- [Config](https://github.com/evantahler/actionHero/wiki/Config)
- [Exceptions](https://github.com/evantahler/actionHero/wiki/Exceptions)
- [Logging](https://github.com/evantahler/actionHero/wiki/Logging)
- [Development Mode](https://github.com/evantahler/actionHero/wiki/Development-Mode)
- [Chat Rooms & connection-to-connection communication](https://github.com/evantahler/actionHero/wiki/Chat)
- [Flat File server](https://github.com/evantahler/actionHero/wiki/File-Server)
- [Extending actionHero](https://github.com/evantahler/actionHero/wiki/Extending-actionHero)

## Client Types
- [Web](https://github.com/evantahler/actionHero/wiki/Web-Clients)
- [TCP](https://github.com/evantahler/actionHero/wiki/TCP-Clients)
- [WebSockets](https://github.com/evantahler/actionHero/wiki/Web-Socket-Clients)

## Internal Method Documentation
- [Internal Methods](https://github.com/evantahler/actionHero/wiki/Internal-Methods)

## Templates and Examples
- [Example Actions](https://github.com/evantahler/actionHero/wiki/Examples:-Actions)
- [Example Tasks](https://github.com/evantahler/actionHero/wiki/Examples:-Tasks)
- [Custom Initializers](https://github.com/evantahler/actionHero/wiki/Custom-Initializers)
- [RESTful routing](https://github.com/evantahler/actionHero/wiki/Examples:-RESTful-routing)
- actionCluster Servers [[1](https://github.com/evantahler/actionHero/blob/master/examples/servers/actionHero_cluster_peer_1.js)] [[2](https://github.com/evantahler/actionHero/blob/master/examples/servers/actionHero_cluster_peer_2.js)] [[3](https://github.com/evantahler/actionHero/blob/master/examples/servers/actionHero_cluster_peer_3.js)]
- [Using node.js clusters to manage a node](https://github.com/evantahler/actionHero/blob/master/scripts/actionHeroCluster)