```javascript

	////////////////////////////////////////////////////////////////////////////
	// Sessions 
	
	var init(api, next){
	
	  api.session = {
	    prefix: "__session",
	    sessionExipreTime: 1000 * 60 * 60 // 1 hour
	  };
	
	  api.session.save = function(api, connection, next){
	    var key = api.session.prefix + "-" + connection.id;
	    var value = connection.session;
	    api.cache.save(api, key, value, api.session.sessionExipreTime, function(){
	      api.cache.load(api, key, function(savedVal){
	        // console.log(savedVal);
	        if(typeof next == "function"){ next(); };
	      });
	    });
	  }
	
	  api.session.load = function(api, connection, next){
	    var key = api.session.prefix + "-" + connection.id;
	    api.cache.load(api, key, function(value){
	      connection.session = value;
	      next(value);
	    });
	  }
	
	  next();
	}
		
```