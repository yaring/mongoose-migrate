## mongoose-migrate-2

Node.js migration framework that uses MongoDB to keep track of migrations.

[![NPM](https://nodei.co/npm/mongoose-migrate-2.png?compact=true)](https://www.npmjs.com/package/mongoose-migrate-2)

This is a fork of [madhums/mongoose-migrate](https://github.com/madhums/mongoose-migrate) with two changes:

* Eliminate the need to configure Mongoose schema and model name. Model is automatically named `Migration`.
* Config file is a Node module. This enables things like reading database URLs from environment variables instead of hard-coding in the JSON file.

## Configuration

Create a config file with one database URL for each node environment, i.e. `process.env.NODE_ENV`.

```js
// Path : ./migrations/config.js
module.exports = {
  development : devDbUrl,
  test        : testDbUrl,
  production  : prodDbUrl
}
```

## Usage

```sh
$ MIGRATION_CONFIG_PATH=./migrations/config.js mongoose-migrate-2 [options] [command]

Options:

   -c, --chdir <path>   change the working directory

Commands:

   down             migrate down
   up               migrate up (default)
   create [title]   create a new migration file with optional [title]
```

## Creating Migrations

To create a migration, execute `mongoose-migrate-2 create` with an optional title. `mongoose-migrate-2` will create a node module within `./migrations/` which contains the following two exports:

    exports.up = function(next){
      next();
    };

    exports.down = function(next){
      next();
    };

All you have to do is populate these, invoking `next()` when complete, and you are ready to migrate!

For example:

    $ migrate create add-pets
    $ migrate create add-owners

The first call creates `./migrations/{timestamp in milliseconds}-add-pets.js`, which we can populate:

      var db = require('./db');

      exports.up = function(next){
        db.rpush('pets', 'tobi');
        db.rpush('pets', 'loki');
        db.rpush('pets', 'jane', next);
      };

      exports.down = function(next){
        db.rpop('pets');
        db.rpop('pets');
        db.rpop('pets', next);
      };

The second creates `./migrations/{timestamp in milliseconds}-add-owners.js`, which we can populate:

      var db = require('./db');

      exports.up = function(next){
        db.rpush('owners', 'taylor');
        db.rpush('owners', 'tj', next);
      };

      exports.down = function(next){
        db.rpop('owners');
        db.rpop('owners', next);
      };

## Running Migrations

When first running the migrations, all will be executed in sequence.

    $ migrate
    up : migrations/1316027432511-add-pets.js
    up : migrations/1316027432512-add-jane.js
    up : migrations/1316027432575-add-owners.js
    up : migrations/1316027433425-coolest-pet.js
    migration : complete

Subsequent attempts will simply output "complete", as they have already been executed in this machine. `mongoose-migrate-2` knows this because it stores the current state in MongoDB.

    $ migrate
    migration : complete

If we were to create another migration using `mongoose-migrate-2 create`, and then execute migrations again, we would execute only those not previously executed:

    $ migrate
    up : migrates/1316027433455-coolest-owner.js

You can also run migrations incrementally by specifying a migration.

    $ migrate up 1316027433425-coolest-pet.js
    up : migrations/1316027432511-add-pets.js
    up : migrations/1316027432512-add-jane.js
    up : migrations/1316027432575-add-owners.js
    up : migrations/1316027433425-coolest-pet.js
    migration : complete

This will run up-migrations upto (and including) `1316027433425-coolest-pet.js`. Similarly you can run down-migrations upto (and including) a specific migration, instead of migrating all the way down.

    $ migrate down 1316027432512-add-jane.js
    down : migrations/1316027432575-add-owners.js
    down : migrations/1316027432512-add-jane.js
    migration : complete

## License

(The MIT License)

Copyright (c) 2011 TJ Holowaychuk <tj@vision-media.ca>

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the 'Software'), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
