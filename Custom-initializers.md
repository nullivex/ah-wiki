Here we will collect example actionHero initializers.

## Format

To use a custom initilizer, create a `initializers` directory in your project.  Ensure that the file name matches the export, IE:

**initStuff.js**

	exports.initStuff = function(api, next){
	  
	  api.stuff = {}; // now api.stuff is globally available to my project
	  api.stuff.magicNumber = 1234;
	
	  next();
	}

You can generate a file of this type with `npm run-script actionHero generateInitializer`

## Initializers: 
- [[Init-mysql]] - Use sequilize.js within an initilizer
- [[Init-session]] - use the cache methods and `connection.id` to easily make some session management helpers.