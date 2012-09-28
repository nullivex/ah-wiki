```javascript

	////////////////////////////////////////////////////////////////////////////
	// DB setup
	//
	// You can add DB specific by adding your task to the api.taks object
	// Your DB init function should be called init and be exported.  init = function(api, next)
	// Name your DB init file the same thing you want folks to use in api.configData.database.type
	
	var init = function(api, next){
		api.mysql = require('mysql');
		api.SequelizeBase = require("sequelize");
		var rawDBParams = {
		  user: api.configData.database.username,
		  password: api.configData.database.password,
		  port: api.configData.database.port,
		  host: api.configData.database.host
		};
		api.rawDBConnction = api.mysql.createClient(rawDBParams);
		api.rawDBConnction.query('USE '+api.configData.database.database, function(e){
			if(e){
				api.log(" >> error connecting to database `"+api.configData.database.database+"`, exiting", ["red", "bold"]);
				api.log(JSON.stringify({database: rawDBParams.database, user: rawDBParams.user, host: rawDBParams.host, port: rawDBParams.port, password: "[censored]"}));
				process.exit();	
			}else{
				api.dbObj = new api.SequelizeBase(api.configData.database.database, api.configData.database.username, api.configData.database.password, {
					host: api.configData.database.host,
					port: api.configData.database.port,
					logging: api.configData.database.consoleLogging
				});
	
				api.models = {};
				api.seeds = {};
				api.modelsArray = [];
	
				var modelsPath = process.cwd() + "/models/";
				api.path.exists(modelsPath, function (exists) {
				  	if(!exists){
				  		var defaultModelsPath = process.cwd() + "/node_modules/actionHero/models/";
				  		api.log("no ./modles path in project, loading defaults from "+defaultModelsPath, "yellow");
						modelsPath = defaultModelsPath;
					}
	
					api.fs.readdirSync(modelsPath).forEach( function(file) {
						var modelName = file.split(".")[0];
						api.models[modelName] = require(modelsPath + file)['defineModel'](api);
						api.seeds[modelName] = require(modelsPath + file)['defineSeeds'](api);
						api.modelsArray.push(modelName); 
						api.log("model loaded: " + modelName, "blue");
					});
					api.dbObj.sync().on('success', function() {
						for(var i in api.seeds){
							var seeds = api.seeds[i];
							var model = api.models[i];
							if (seeds != null){
								api.utils.DBSeed(api, model, seeds, function(seeded, modelResp){
									if(seeded){ api.log("Seeded data for: "+modelResp.name, "cyan"); }
								});
							}
						}
						api.log("mySQL DB conneciton sucessfull and Objects mapped to DB tables", "green");
						next();
					}).on('failure', function(error) {
						api.log("trouble synchronizing models and DB.  Correct DB credentials?", "red");
						api.log(JSON.stringify(error));
						api.log("exiting", "red");
						process.exit();
					})
				});
			}
		});
		
		////////////////////////////////////////////////////////////////////////////
		// DB Seeding
		api.utils.DBSeed = function(api, model, seeds, next){
			model.count().on('success', function(modelsFound) {
				if(modelsFound > 0)
				{
					next(false, model);
				}else{
					var chainer = new api.SequelizeBase.Utils.QueryChainer;
					for(var i in seeds){
						seed = seeds[i];
						chainer.add(model.build(seed).save());
					}
					chainer.run().on('success', function(){
						next(true, model);
					}).on('failure', function(errors){
						for(var i in errors){
							api.log(errors[i], "red");
						}
						next(false, model);
					});
				}
			});
		}
		
	}
	
	exports.init = init;


```

sequelize.js models, which like in `/models/` look like this:


```javascript

	function defineModel(api)
	{
		var model = api.dbObj.define('User', {
			screenName: { type: api.SequelizeBase.STRING, allowNull: false, defaultValue: null, unique: true, autoIncrement: false},
			email: { type: api.SequelizeBase.STRING, allowNull: false, defaultValue: null, unique: true, autoIncrement: false},
			image: { type: api.SequelizeBase.STRING, allowNull: true, defaultValue: null, unique: true, autoIncrement: false},
			passwordHash: { type: api.SequelizeBase.STRING, allowNull: false, defaultValue: null, unique: false, autoIncrement: false},
			passwordSalt: { type: api.SequelizeBase.STRING, allowNull: false, defaultValue: null, unique: false, autoIncrement: false},
			isAI: { type: api.SequelizeBase.BOOLEAN, allowNull: false, defaultValue: 0, unique: false, autoIncrement: false},
		});	
		return model;
	}
	
	function defineSeeds(api){
		// the password is `password`
		var seeds = [
				{"screenName": "AI_Player_1", "email":"ai_player_1@me.com", "passwordHash": "x", "passwordSalt":"x", "isAI":true},
				{"screenName": "AI_Player_2", "email":"ai_player_2@me.com", "passwordHash": "x", "passwordSalt":"x", "isAI":true},
				{"screenName": "AI_Player_3", "email":"ai_player_3@me.com", "passwordHash": "x", "passwordSalt":"x", "isAI":true},
			];
		return seeds;
	}
	
	exports.defineModel = defineModel;
	exports.defineSeeds = defineSeeds;
	
```