### Express Middleware - BodyParser

1. BodyParser - Pass Information coming from a Form. Lets define the Form
2. BodyParser is now part of the express package so no need to import it separately 

```html
<body>
  <h1>Band Name Generator</h1>
  <form action="/submit" method="POST">
    <label for="street">Street Name:</label>
    <input type="text" name="street" required>
    <label for="pet">Pet Name:</label>
    <input type="text" name="pet" required>
    <input type="submit" value="Submit">
  </form>
</body>
```

1. Lets consume it

```js
import express from "express";
import bodyParser from "body-parser";
const app = express();
app.use(bodyParser.urlencoded({ extended: true })); // or app.use(express.urlencoded({ extended: true }));
const port = 3000;
app.post("/submit", (req, res) => {
  console.log(req.body);
  res.send(req.body);
});
app.listen(port, () => {
  console.log(`Listening on port ${port}`);
});
```

1. Output Log & Response ->

```txt
{ street: 'Atos', pet: 'Bad' }
```

1. We can also send the request from Postman as well 

```bash
$ curl --request POST \
  --url http://localhost:3000/submit \
  --header 'Content-Type: application/x-www-form-urlencoded' \
  --data street=Downtown \
  --data pet=Dog
{"street":"Downtown","pet":"Dog"}
```
#### Serve HTML file

```js
import express from "express";
import { dirname } from "path";
import { fileURLToPath } from "url";
const __dirname = dirname(fileURLToPath(import.meta.url));
const app = express();
const port = 3000;

app.get("/", (req, res) => {
  res.sendFile(__dirname + "/public/index.html");
});
app.listen(port, () => {
  console.log(`Listening on port ${port}`);
});

```


#### Types of Middleware

1. Pre-processing ( Body Parser )
2. Auth
3. Error Handling
4. Logging ( Morgan )

#### Using Morgan with Express

```js
import express from "express";
import { dirname } from "path";
import { fileURLToPath } from "url";
import bodyParser from "body-parser";
import morgan from "morgan";

const app = express();

app.use(bodyParser.urlencoded({ extended: true }));
app.use(morgan("combined"));

const __dirname = dirname(fileURLToPath(import.meta.url));

const port = 3000;

app.get("/", (req, res) => {
  res.sendFile(__dirname + "/public/index.html");
});

app.post("/submit", (req, res) => {
  console.log(req.body);
  res.send(req.body);
});

app.listen(port, () => {
  console.log(`Listening on port ${port}`);
});

```


Logs ->

```bash
{ street: 'Downtown', pet: 'Dog' }
::1 - - [15/Sep/2026:04:52:21 +0000] "POST /submit HTTP/1.1" 200 33 "-" "bruno-runtime/4.1.0"
{ street: 'Downtown', pet: 'Dog' }
::1 - - [15/Sep/2026:04:53:03 +0000] "POST /submit HTTP/1.1" 200 33 "-" "bruno-runtime/4.1.0"
{ street: 'Atos', pet: 'Bad' }
::1 - - [15/Sep/2026:04:53:22 +0000] "POST /submit HTTP/1.1" 200 29 "http://localhost:3000/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/153.0.0.0 Safari/537.36"
::1 - - [15/Sep/2026:04:53:22 +0000] "GET /.well-known/appspecific/com.chrome.devtools.json HTTP/1.1" 404 187 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/153.0.0.0 Safari/537.36"]
```


## Create our Own Middleware

1. https://expressjs.com/en/5x/guide/writing-middleware/

```js
app.use((req, res, next) => {
  console.log(req.method);
  next();
});

// OR
app.use(privateLoggerMiddleware);
function privateLoggerMiddleware(req, res, next) {
  console.log(`Private Logger Middleware : ${req.url} : ${req.method}`);
  next();
}
```

1. next() ensure the operation continues.
#### Serving Static Files

1. https://expressjs.com/en/5x/starter/static-files/
2. Static files = CSS, images, client JS, fonts. No EJS processing, sent as-is.
3. `app.use(express.static("public"));` serves `public/` at root `/`.

```js
// MagixApp\index.js - relative (works locally, breaks if cwd changes)
app.use(express.static("public"));

// MagixApp\index.js - absolute (same locally, safe in prod)
import { dirname } from "path";
import { fileURLToPath } from "url";
const __dirname = dirname(fileURLToPath(import.meta.url));
app.use(express.static(__dirname + "/public"));
```

```
MagixApp\public\styles\layout.css  -> http://localhost:3000/styles/layout.css
MagixApp\public\styles\content.css -> http://localhost:3000/styles/content.css
MagixApp\public\images\cat.jpeg    -> http://localhost:3000/images/cat.jpeg
```

```html
<!-- MagixApp\views\partials\header.ejs:9-10 -->
<link rel="stylesheet" href="/styles/layout.css">
<link rel="stylesheet" href="/styles/content.css">
<!-- leading / = from root, not relative to current route (/about, /contact) -->
```

Notes:
- No `/public` in the URL. `express.static("public")` strips it. `href="/public/styles/..."` = 404.
- Order matters: put `app.use(express.static(...))` before routes.
- Use absolute path in production: `app.use(express.static(__dirname + "/public"))`.
- Virtual prefix if needed: `app.use("/static", express.static("public"))` -> then `href="/static/styles/layout.css"`.

#### res.send() Does Not Stop Execution

1. `res.send()` / `res.render()` / `res.json()` / `res.redirect()` only send the response — code below still runs.
2. Use `return res.send(...)` to stop, else risk "headers already sent" errors.

```js
// bad - query still runs after send
res.send("Done");
await dbConnection.query(...);

// good
return res.send("Done");
```