## Who is the actionHero?
actionHero is a [node.js](http://nodejs.org) **API framework** for both **tcp sockets**, **web sockets**, and **http clients**.  The goal of actionHero are to create an easy-to-use toolkit for making **reusable** & **scalable** APIs.  clients connected to an actionHero server can **consume the api**, **consume static content**, and **communicate with each other**.

actionHero servers can process both requests and tasks (delayed actions like `send e-mail` or other background jobs).  actionHero servers can also run in a cluster (on the same or multiple machines) to work in concert to handle your load.

The actionHero API defines a single access point and accepts GET, POST, PUT and DELETE input along with persistent connection via TCP or web sockets. You define **Actions** which handle input and response, such as "userAdd" or "geoLocate". HTTP, HTTPS, and TCP clients can all use these actions.  The actionHero API is not inherently "RESTful" (which is meaningless for persistent socket connections) but can be extended to be so if you wish.

actionHero will also serve static files for you, but actionHero is not a 'rendering' server (like express or rails).

Use the SideBar to get around.

---

**note**: This wiki will always reflect the master branch of actionHero, and therefore may be slightly ahead of the latest release on NPM.

---

#### Looking for other Languages?

- [US English (en_US)](https://github.com/evantahler/actionHero/wiki) maintained by [@evantahler](https://github.com/evantahler)
- [Simplified Chinese (zh_HANS)](https://github.com/jacobbubu/actionHero-wiki-zh-hans/wiki) maintained by [@jacobbubu](https://github.com/jacobbubu)

---

![image](https://raw.github.com/evantahler/actionHero/master/public/logo/actionHero.png)