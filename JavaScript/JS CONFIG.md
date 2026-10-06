#### CJS VS ESM ( Modern )

1. CJS - Common JS
2. File extension: `.js` (when `"type": "commonjs"` or no `type` field in package.json) or `.cjs`

```js
// math.js
module.exports = { add: (a, b) => a + b };

// app.js
const { add } = require('./math');
```

1. ESM - ES Modules
2. File extension: `.mjs`, or `.js` with `"type": "module"` in package.json

```js
// math.mjs
export const add = (a, b) => a + b;

// app.mjs
import { add } from './math.mjs';
```

#### Nodemon

Best to install globally -> `npm i -g nodemon`.Then start the app using `nodemon index.js`. This will now watch for changes.

#### Prettier 

Install prettier plugin. Set is as default .Create a folder -> `.vscode` -> Create a file inside it -> `settings.json` 

```json
{
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": true
}
```

For Prettier config -> Navigate to its official website and just copy paste. Or create a file named -> `.prettierrc.js`

```js
/**
 * @see https://prettier.io/docs/configuration
 * @type {import("prettier").Config}
 */
const config = {
  trailingComma: "es5",
  tabWidth: 4,
  semi: true,
  singleQuote: true,
};
export default config;
```

#### Directory Navigator

1. Say your want to navigate to a file. This is the best way.

```js
import express from "express";
import { dirname } from "path";
import { fileURLToPath } from "url";
const __dirname = dirname(fileURLToPath(import.meta.url));

app.get("/", (req, res) => {
  res.sendFile(__dirname + "/public/index.html");
});
```

## PG Connection

```js
import pg from "pg";

const dbConnection = new pg.Client({
    user: "xxxxxxxx",
    host: "xxxxxxxxxx.us-east-2.aws.xx.tech",
    database: "xxxxx",
    password: "xxxxxx",
    port: "5432",
    ssl: { rejectUnauthorized: false }, // SSL
});

const dbConnection = new pg.Client({
    connectionString:
         "sxxxxx",
});

dbConnection.connect();

let captials;

dbConnection.query("SELECT * FROM capitals", (err, res) => {
    if (err) {
        console.error("Error executing query", err);
    } else {
        // console.log(res.rows);
        captials = res.rows;
    }
    dbConnection.end();
    console.log(captials[0]);
});

```


#### Insert SQL Data

```js
 await dbConnection.query(
      "INSERT INTO users (email, password) VALUES ($1, $2)",
      [email, password],
    );
    
await dbConnection.query(
    "select * from users where email = ($1)",
    [email],
  );
```

