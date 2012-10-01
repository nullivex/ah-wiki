```javascript

var action = {};

/////////////////////////////////////////////////////////////////////
// metadata
action.name = "generateProjectZip";
action.description = "I will generate a fresh actionHero project from NPM and present it as a ZIP";
action.inputs = {
	"required" : [],
	"optional" : ["provider"]
};
action.outputExample = {}

/////////////////////////////////////////////////////////////////////
// functional
action.run = function(api, connection, next){

	var seed = new Date().getTime() + "_" + connection.remoteIP;
	var working_dir = "/tmp/" + seed;
	var zip = "/tmp/" + seed + ".zip";

	function exec_with_log(api, cmd, next){
		api.log("[ cmd ] "+cmd);
		api.utils.shellExec(api, cmd, function(){
			next();
		})
	}

	api.async.series({
		ensure_dirs_exist: function(cb){ exec_with_log(api, "mkdir -p "+working_dir, cb); },
		dowload: function(cb){ exec_with_log(api, "cd " + working_dir + " && npm install actionHero", cb); },
		generate: function(cb){ exec_with_log(api, "cd " + working_dir + " && npm run-script actionHero generate", cb); },
		zip: function(cb){ exec_with_log(api, "cd " + working_dir + " && zip " + zip + " * -r", cb); },
		clean_folder: function(cb){ exec_with_log(api, "rm -rf " + working_dir, cb); },
		send: function(cb){ api.fileServer.sendFile(api, zip, connection, function(conn){
						cb();
					}); },
		clean_zip: function(cb){ exec_with_log(api, "rm " + zip, cb); },
		done: function(){
			next(connection, false);
		}
	});
};

/////////////////////////////////////////////////////////////////////
// exports
exports.action = action;

```