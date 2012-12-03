initializers are run before your server boots.  Here is where you include any new modules or add custom code which you want to be availalbe to all the rest of your actionHero server. 

Initializers are loaded alpabetically. 

## Format

To use a custom initializer, create a `initializers` directory in your project.  Ensure that the file name matches the export, IE:

**initStuff.js**

	exports.initStuff = function(api, next){
	  
	  api.stuff = {}; // now api.stuff is globally available to my project
	  api.stuff.magicNumber = 1234;
	
	  next();
	}

You can generate a file of this type with `actionHero generateInitializer`

## Initializer Examples: 
- [[Init-mysql]] - Use sequilize.js within an initializer
- [[Init-session]] - use the cache methods and `connection.id` to easily make some session management helpers.