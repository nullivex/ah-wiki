# Middleware 
## Middleware (aka action pre and post processing)

actionHero provides hooks for you to execute custom code both before and after the excecution of all actions.  You do this by adding to the `api.actions.preProcessors` and `api.actions.postProcessors` array.  Functions you add here will be excecuted in order on the connection.  

This is a great place to write atutentication logic or custom loggers.

preProcessors, like actions themselves, return the connection and a `toProcess` flag.  Setting `toProcess` to false will block the excecution of an action.  You can operate on `connection.response` and `connection.error`, just like within an action to create messages to the client.

**preProcessors** are provided with `connection`, `actionTemplate`, and `next`.  They are expected to return (connection, toProcess)
**postProcessors** are provided with `connection`, `actionTemplate`, and `next`.  They are expected to return (connection)

Some Examples:

```javascript

// a preProcessor to check if a userId is provided:

api.actions.preProcessors.push(function(connection, actionTemplate, next){
  if(connection.params.userId == null){
    connection.error = "All actions require a userId";
    next(connection, false);
  }else{
    next(connection, true);
  }
});

// a postProcessor to append the action's description to the response body

api.actions.postProcessors.push(function(connection, actionTemplate, next){
  connection.response._description = actionTemplate.description;
  next(connection);
});

```