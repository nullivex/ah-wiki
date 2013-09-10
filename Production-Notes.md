# Production Notes
A collection of thoughts on deploying actionHero apps

## Forever
When deploying your app, it is a good idea to demonize your application with a tool like [forever](https://github.com/nodejitsu/forever).  Forever is a process manager which will:

- demonize (background) your app
- collect the output to a file
- manage log files
- and more

While you can daemonize both the actionHero server and clusterManager, they will not restart automatically if something goes terribly wrong.  Here is a quick guide to handling production deployments:

1. install forever globally `npm install forever -g`
2. instruct forever to manage an actionHero cluster `forever start ./node_modules/actionHero/bin/actionHero start [args]`
3. when you deploy, you can instruct the actionHero cluster process to gracefully restart (rather than using `forever restart` which will actually stop and start your server) `kill -s USR2 [pid]` (both `actionHero start` and `actionHero startCluster`) respond to the SIGUSR2 signal as a restart.

## Number of workers

When choosing the number of workers for your actionHero cluster, choose at least 1 less than the number of CPUs available.  If you have a "burstable" architecture (like a Joyent smart machine), opt for the highest number of 'consistent' CPUs you can have, meaning a number of CPUs that you will always have available to you.  

You never want more workers than you can run at a time, or else you will actually be slowing dow the execution of all processes

Of course, not going in to swap memory is more important than utilizing all of your CPUs, so if you find yourself running out of ram, reduce the number of workers! 

## Global Packages

It's probably best to avoid installing any global packages.  This way, you won't have to worry about conflicts, and your project can be kept up to date more simply.  When you use npm to install a local package the package's binaries are always copied into `./node_modules/.bin`. If you add forever as a dependency to your project, you can run both forever and actionHero locally like so:

`.node_modules/.bin/forever .node_modules/.bin/actionHero startCluster [args]`

You can add local references to your $PATH like so to use these local binaries:

`export PATH=$PATH:node_modules/.bin`

## Misc

Be sure to change `api.configdata.general.serverToken` to something unique for your application