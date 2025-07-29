# USE SEQUELIZE WITH NEXTJS

## Required dependencies

To run migrations we need sequelize-cli and for envirorments variables dotenv-cli

```sh
bun install --save-dev sequelize-cli dotenv-cli
```

Now we needs:

```bash
bun install oracledb sequelize sequelize-typescript --save
```

Now we need to init sequelize

```bash
bunx sequelize-cli init
```

Sequelize-cli init command will generate necessary files, but unfortunately it will spread in all those files in the root folder. It's better to re-organize the root creating a database folder to the root of our project

## Create a config file

Inside the config folder delete the default config.json and create a config.mjs file with the following code:

```mjs
/* eslint-disable import/no-anonymous-default-export */
export const options = {
  username: process.env.DB_USERNAME,
  password: process.env.DB_PASSWORD,
  database: process.env.DB_NAME,
  host: process.env.DB_HOST,
  port: Number(process.env.DB_PORT),
  dialect: "oracle",
  logging: process.env.NODE_ENV === "development" ? console.log : false,
  migrationStorageTableName: "migrations",
};

if (process.env.NODE_ENV === "production") {
  options.dialectOptions = {
    ssl: {
      rejectUnhautorized: true,
    },
  };
}

export default {
  development: options,
  test: options,
  production: options,
};
```

Now in the root of the project create a .sequelizerc file with the following code:

```ts
const path = require("path");

module.exports = {
  config: path.resolve("database", "config/config.mjs"),
  "seeders-path": path.resolve("database", "seeders"),
  "migrations-path": path.resolve("database", "migrations"),
};
```

Now, inside the package.json add the following lines in the scripts section:

```json
    "migration:create": "dotenv -- sequelize-cli migration:create",
    "migrate": "dotenv -- sequelize-cli db:migrate",
    "migrate:rollback": "dotenv -- sequelize-cli db:migrate:undo",
    "migrate:rollback:all": "dotenv -- sequelize-cli db:migrate:undo:all"
```

## Create the first migration

Run this command in the terminal:

```bash
bun run migration:create -- --name create-users-table
```
