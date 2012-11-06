The first thing to do is to make your own ./actions and ./tasks folder.  If you like the default actions, feel free to copy them in.  A common practice to extend the API is to add new classes which are not actions, but useful to the rest of the api.  The api variable is globally accessible to all actions within the API, so if you want to define something everyone can use, add it to the api object.  In the quickstart example, if we wanted to create a method to generate a random number, we could do the following:

```javascript
	
	function initFunction(api, next){
		api.utils.randomNumber = function(){
			return Math.random() * 100;
		};
	};
	
	var actionHero = require("actionHero").actionHero;
	actionHero.start({initFunction: initFunction}, function(error, api){
		api.log("Loading complete!", ['green', 'bold']);
	});
```

Now `api.utils.randomNumber()` is available for any action to use!  It is important to define extra methods in a setter function which is passed to the API on boot via `params.initFunction`. Even though the api object is returned to you, setting globally-available functions after initialization may not propagate to the parts of actionHero.

You can add initializers to your project to add things like mySQL support.  Check out out list of [actions](example-actions) and [tasks](example-tasks) for examples.