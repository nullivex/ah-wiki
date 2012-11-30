# Production Notes
A collection of thoughts on deploying actionHero apps

## Forever
When deploying your app, it is a good idea to demonize your application with a tool like [forever](https://github.com/nodejitsu/forever).  Forever is a process manager which will:

- demonize (background) your app
- collect the output to a file
- manage log files
- and more

Here is a quick guide to handling production deployments:

1. install forever globally `npm install forever -g`
2. instruct forever to manage an actionHero cluster `forever ./node_modules/actionHero/bin/actionHero startCluster [args]`
3. when you deploy, you can instruct the actionHero cluster process to gracefully restart (rather than using `forever restart` which will actually stop and start your server) `kill -s USR2 [pid]`

## Number of workers

When choosing the number of workers for your actionHero cluster, choose at least 1 less than the number of CPUs available.  If you have a "bistable" architecture (like a Joyent smart machine), opt for the highest number of consistent CPUs you can have.  

You never want more workers than you can run at a time, or else you will actually be slowing dow the execution of all processes

Of course, not going in to swap memory is more important than utilizing all of your CPUs, so if you find yourself running out of ram, reduce the number of workers! 