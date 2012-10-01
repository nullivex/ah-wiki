# Tasks

Tasks are background jobs meant to be run asynchronously from a request.  With actionHero, there is no need to run a separate job processing/queuing process.  Using the node.js event loop, background tasks can be processed in-line with web requests in a non-blocking way.  Tasks are built like actions, but they can be run as called or periodically.  Tasks can be run on every node in the actionCluster or just one.  There is one task which is core to action hero `runAction`, but there are [a number of example tasks provided](Example-tasks).

You can create you own tasks by placing them in a `./tasks/` folder at the root of your application.  Like actions, all tasks have some required metadata:

* `task.name`: The unique name of your task
* `task.description`: a description
* `task.scope`: "**any**" or "**all**".  Should a single actionCluster server (any) run this task, or should all of them? For example, `pingSocketClients` is run by all peers in the action cluster (because we want all clients to be pinged), but if you had a task to clean old sessions from your database or send an email, you would only want a single node to do that.
* `task.frequency`: In milliseconds, how often should I run?.  Setting me to 0 will cause me not to run automatically, but I can still be run with `api.task.run`

To enqueue a task (the normal way of doing things) use `api.tasks.enqueue(api, taskName, runAtTime, params)`.  To run a task in the future, set runAtTime, otherwise leave it null or set in the past.


As stated above, any task can also be called programmatically with `api.tasks.run(api, taskName, params, next)`.

An example Task:

```javascript

	var task = {};
	
	/////////////////////////////////////////////////////////////////////
	// metadata
	task.name = "sayHello";
	task.description = "I am a demo task which will be run only on one peer";
	task.scope = "any";
	task.frequency = 1000;
	
	/////////////////////////////////////////////////////////////////////
	// functional
	task.run = function(api, params, next){
		api.log("----- Hi There! ----", "green");
		next(true);
	};
	
	/////////////////////////////////////////////////////////////////////
	// exports
	exports.task = task;
```
	
This task will be run every ~1 second on the first peer to be free after that one second has elapsed.  It is important to note that the `runAt` time is setting the when the task is 'allowed' to be run, not explicitly when it will be run.  Due to this, it is highly likely that your task will be run slightly after the set runAt time.
