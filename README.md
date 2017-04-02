# RESTful API Guide

## Setting up the Node project skeleton
Start by creating the project folder and changing directory into it by typing`mkdir restful_API && cd restful_API`

Once in the folder, initialize npm with`npm init`
Just spam enter for defaults or change whatever you think is necessarry.

Once this is done you should of generated a package.json which should look like

	{
  	"name": "example",
  	"version": "1.0.0",
  	"description": "An example package.json",
  	"main": "index.js",
  	"scripts": {
    		"test": "echo \"Error: no test specified\" && exit 1"
  	},
  	"author": "Your Name",
  	"license": "ISC"
	}


installs dependencies for your API
`npm install --save express mongoose body-parser`
As well as the developer dependencies
`npm install --save-dev nodemon eslint babel-cli babel-core babel-eslint babel-preset-es2015 babel-preset-stage-0`


This should of added some lines to the package.json

	{
 	"name": "example",
 	"version": "1.0.0",
 	"description": "An example package.json",
 	"main": "index.js",
 	"scripts": {
 		"test": "echo \"Error: no test specified\" && exit 1"
 	},
 	"author": "Your Name",
 	"license": "ISC",
 	"dependencies": {
		 "body-parser": "^1.17.1",
		 "express": "^4.15.2",
		 "mongoose": "^4.9.2"
	  },
	  "devDependencies": {
		    "babel-cli": "^6.24.0",
		    "babel-core": "^6.24.0",
		    "babel-eslint": "^7.2.1",
		    "babel-preset-es2015": "^6.24.0",
		    "babel-preset-stage-0": "^6.22.0",
		    "eslint": "^3.19.0",
		    "nodemon": "^1.11.0"
		  }
	}

Add a section for scripts and eslintConfig and fill them in with:

	{
	"name": "restful-api",
	"version": "0.0.1",
	"description": "Udemy tute",
	"main": "dist",
	"scripts": {
		"dev": "NODE_ENV=development nodemon -w src --exec \"babel-node src --presets es2015,stage-0\"",
		"build": "babel src -s -D -d dist --presets es2015,stage-0",
		"prestart": "npm run -s build",
		"start": "NODE_ENV=production pm2 start dist",
		"lint": "eslint src",
		"test": "echo \"Error: no test specified\" && exit 1"
	},
	"eslintConfig": {
		"parserOptions": {
			 "ecmaVersion": 7,
			"sourceType": "module"
		},
		"env": {
			"node": true
		},
		"rules": {
			"no-console": 0,
			"no-unused-vars": 1
			}
		},
	"author": "Willdos",
	"license": "ISC",
	"dependencies": {
		"body-parser": "^1.17.1",
		"express": "^4.15.2",
		"mongoose": "^4.9.2"
	},
	"devDependencies": {
		"babel-cli": "^6.24.0",
		"babel-core": "^6.24.0",
		"babel-eslint": "^7.2.1",
		"babel-preset-es2015": "^6.24.0",
		"babel-preset-stage-0": "^6.22.0",
		"eslint": "^3.19.0",
		"nodemon": "^1.11.0"
		}
	}



create babel config with `touch .babelrc` and add

	{
		"presets": [
			"es2015",
			"stage-0"
		]
	}

Now the npm can run the following commands which will execute what has been defined in the package.json
- `npm run build`: Will use babel to compile ES6 code into ES5, results can be seen in the `dist` folder
- `npm run lint`: Runs eslint to help maintain and tidy code to a standard define in  the eslintConfig section of the package.json *(can seperate into it's own .eslintrc file)*
- `npm run prestart`: Runs build apparently...
- `npm run dev`: Starts the server in dev mode which will automaticalluy refresh changes as they're made

We now need to create a source folder where we will store our ES6 codebase `mkdir src`
We also should create the main index.js file ` touch src/index.js`
Inside this new index.js file, add the following:

	import http from 'http';
	import express from 'express';
	import bodyParser from 'body-parser';
	import mongoose from 'mongoose';

	import config from './config';
	import routes from './routes';

	let app = express();
	app.server = http:creatServer(app);

	//	middleware

	//	passport config

	//	api routes v1
	app.use('/v1', routes);

	app.server.listen(config.port);
	console.log(`Started on port ${app.server.address().port}`);

	export default app;


## Setting up MongoDB fort Node API

Create the db file with `touch src/dbjs` and add:

	import mongoose from 'mongoose';
	import config from 'config';

	export default callback => {
		let db = mongoose.connect(config.mongoUrl);
		callback(db);
	}



Now create the folders for config, middleware and routes `mkdir src/config src/middleware src/routes`. In each folder create an index.js file

**config/index.js**

	export default {
		"port": 3005,
		"mongoUrl": "mongodb://localhost:27017/restful-api"
	}

**middleware/index.js**

	import { Router } from 'express';

	export default({ config, db }) => {
		let api = Router();

		//	midware

		return api
	}

**routes/index.js**

	import express from 'express';
	import config from '../config';
	import middleware from '../middleware';
	import initializeDb from '../db';

	let router = express();

	//	connect to db
	initializeDb(db => {

		//	internal middleware
		router.use(middleware({ config, db }));
		//	api routes v1
	});

	export default router


By this point your projects structure should look like

**api/**
>**node_modules/**
>**src/**
>>**config/**
>>>*index.js*
>>
>>**middleware/**
>>>*index.js*
>>
>>**routes/**
>>>*index.js*
>>
>>*db.js*
>>*index.js*
>
>*.babelrc (hidden file)*
>*packacge.json*

## Adding data POST requests in Node

Make a controller directory and in it a file named after what it is you will be posting `mkdir src/controller &&  touch src/controller/restaurant.js`

In the controller add code similar to this

	import mongoose from 'mongoose';
	import { Router } from 'express';
	import Restaurant from '../model/restaurant';
	import bodyParser from 'body-parser';

	export default({ config, db }) => {
		let api = Router();

		//	'/v1/restaurant/add'
		api.post('/add', (req, res) => {
			let newRest = new Restaurant();
			newRest.name = req.body.name;

			newRest.save(err => {
				if(err) {
					res.send(err);
				}
				res.json({ message: 'Restaurant saved succesfully' });
			});
		});

		return api;
	}

Althoug we have imported the 'restaurant model' we are yet to actually create one.
  `mkdir src/model &&  touch src/model/restaurant.js`

  Within the model file outline how you would like the schema to appear, below is a brutally simple example

  	import mongoose from 'mongoose';
	let Schema = mongoose.Schema;

	let restaurantSchema = new Schema({
		name: String
	});

	module.exports = mongoose.model('Restaurant', restaurantSchema);


Once this had been all implemented, make sure mongDB is running and open up Postman to test the api endpoint.
When using postman remember to specify this as a POST request with the 'Body' paramters set to raw => JSON. Then enter date that matches the schema such as

	{
	"name": "Fluffy Knuckles Boi"
	}

If the POST comes back as a success, be sure to check it in either robomongo or the mongo cli client.

Underneath that post in the controller you can also add the following to allow for viewing all or viewing one item from the database

	//	/v1/restaurant/ - view all
	api.get('/', (req, res) => {
		Restaurant.find({}, (err, restaurants) => {
			if (err) {
				res.send(err);
			}
			res.json(restaurants);
		});
	});

	//	/v1/restaurant/:id - view one
	api.get('/:id', (req, res) => {
		Restaurant.findById(req.params.id, (err, restaurant) => {
			if (err) {
				res.send(err);
			}
			res.json(restaurant);
		});
	});

In order to add endpoints for update and remove add the following:

	//	/v1/restaurant/:id - Update
	api.put('/:id', (req, res) => {
		Restaurant.findById(req.params.id, (err, restaurant) => {
			if (err) {
				res.send(err)
			}
			restaurant.name = req.body.name;
			restaurant.save(err => {
				if (err) {
					res.send(err);
				}
				res.json({ message: "Restaurant has been unpdated" });
			});
		});
	});

	//	/v1/restaurant/:id - Delete
	api.delete('/:id', (req, res) => {
		Restaurant.remove({
			_id: req.params.id
		}, (err, restaurant) => {
			if(err) {
				res.send(err);
			}
			res.json({ message: "Restaurant Removed" });
		});
	});
