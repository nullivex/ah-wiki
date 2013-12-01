# Production Notes
A collection of thoughts on deploying actionHero apps

## Paths and Envrionments

You can set a few environment variables to affect how actionHero runs:

- `PROJECT_ROOT`: This is useful when deploying actionHero applications on a server where symlinks will change under a running process.  The cluster will look at your symlink `PROJECT_ROOT=/path/to/current_symlink` rather than the absolute path it was started from
- `ACTIONHERO_ROOT`: This can used to set the absolute path to the actionHero binaries
- `ACTIONHERO_CONFIG`: This can be user to set the absolute path to the actionHero config file you wish to use.  This is useful when you might have a `staging.config.json` and a `production.config.json`


## Dameon

When deploying actionHero, you will probably have more than 1 process.  You can use the cluster manager to keep an eye on the workers and manage them

- Start the cluster with 2 workers: `./node_modules/.bin/actionHero startCluster --workers=2`

When deploying new code, you can gracefully restart your workers by sending the `USR2` signal to the cluster manager to signal a reload to all workers.  You don't need to start and stop the cluster-master.  This allows for 0-downtime deployments.  

You may want to set some of the ENV variables above to help with your deployment.

## Number of workers

When choosing the number of workers (`--workers=n`) for your actionHero cluster, choose at least 1 less than the number of CPUs available.  If you have a "burstable" architecture (like a Joyent smart machine), opt for the highest number of 'consistent' CPUs you can have, meaning a number of CPUs that you will always have available to you.  

You never want more workers than you can run at a time, or else you will actually be slowing down the execution of all processes.

Of course, not going in to swap memory is more important than utilizing all of your CPUs, so if you find yourself running out of ram, reduce the number of workers! 

## Global Packages

It's probably best to avoid installing any global packages.  This way, you won't have to worry about conflicts, and your project can be kept up to date more easily.  When using npm to install a local package the package's binaries are always copied into `./node_modules/.bin`. 

You can add local references to your $PATH like so to use these local binaries:

`export PATH=$PATH:node_modules/.bin`

## Security

Be sure to change `api.configdata.general.serverToken` to something unique for your application