## mongoose-migrate-2

Fork of [madhums/mongoose-migrate](https://github.com/madhums/mongoose-migrate) with two changes:

* Config file is a Node module. This enables one to do things like reading database URLs from environment variables instead of hard-coding in the JSON file.
* Eliminate the need to configure Mongoose schema and model name. Model is automatically named `Migration`.

## Installation

```sh
$ npm install mongoose-migrate-2 -g
```
## Usage

First, create a config file with one database URL for each mode.
```js
// Path : ./config/migrations.js
module.exports = {
  development : devDbUrl,
  test        : testDbUrl,
  production  : prodDbUrl,
}
```

Then, run the `mongoose-migrate-2` command.
```sh
$ MIGRATION_CONFIG_PATH=./config/migrations.js mongoose-migrate-2 [options] [command]

Options:

   -c, --chdir <path>   change the working directory

Commands:

   down             migrate down
   up               migrate up (the default command)
   create [title]   create a new migration file with optional [title]
```

Refer to [visionmedia/node-migrate](https://github.com/visionmedia/node-migrate) for instructions to create migrations.

## License

MIT