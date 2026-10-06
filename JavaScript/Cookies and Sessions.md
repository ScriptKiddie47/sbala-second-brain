# Cookies and Sessions (with Passport)

Source: `MagixApp/index.js`, [Passport docs](https://www.passportjs.org/), [express-session](https://expressjs.com/en/resources/middleware/session/)

## Cookie vs Session in one paragraph

- A **cookie** is a small key-value pair stored in the browser and sent with every request.
- A **session** is server-side state keyed by a session ID, which itself lives in a signed cookie (`connect.sid` by default).
- Flow: login → server creates session → browser stores session ID cookie → later requests send the cookie → server looks up `req.user`.

## Stack

| Package | Role |
|---|---|
| **express-session** | Creates `req.session`, signs the session ID cookie |
| **passport** | Auth middleware (`initialize`, `session`, `authenticate`) |
| **passport-local** | Username + password strategy |
| **bcrypt** | Password hashing (never store plain text) |
| **dotenv** | Loads secrets from `.env` |

```js
// MagixApp/index.js:1-8
import passport from "passport";
import { Strategy } from "passport-local";
import session from "express-session";
```

## Order matters

Session must come before `passport.initialize()` / `passport.session()`, and the body parser before any route that reads `req.body`.

```js
// MagixApp/index.js:15-26
app.use(
  session({
    secret: "TOPSECRETWORD",
    resave: false,
    saveUninitialized: true,
  }),
);
app.use(express.urlencoded({ extended: true }));
app.use(express.static("public"));

app.use(passport.initialize());
app.use(passport.session());
```

### Session options

- `secret` — signs the session ID cookie so clients can't forge it. Use `process.env.SESSION_SECRET` in real code, not a hardcoded string.
- `resave: false` — don't rewrite the session to the store if nothing changed.
- `saveUninitialized: true` — create a session (and set the cookie) even before login. Set `false` in production to avoid empty sessions.

## Register — hash, insert, auto-login

```js
// MagixApp/index.js:72-104
app.post("/register", async (req, res) => {
  const email = req.body.username;
  const password = req.body.password;

  const checkResult = await db.query("SELECT * FROM users WHERE email = $1", [email]);

  if (checkResult.rows.length > 0) {
    return res.redirect("/login"); // already registered
  }

  bcrypt.hash(password, saltRounds, async (err, hash) => {
    const result = await db.query(
      "INSERT INTO users (email, password) VALUES ($1, $2) RETURNING *",
      [email, hash],
    );
    const user = result.rows[0];
    req.login(user, (err) => {
      res.redirect("/secrets"); // logged in straight after registering
    });
  });
});
```

Key points: check for existing user first, store only the bcrypt `hash`, `req.login()` establishes the session.

## Login — Passport local strategy

```js
// MagixApp/index.js:64-70
app.post(
  "/login",
  passport.authenticate("local", {
    successRedirect: "/secrets",
    failureRedirect: "/login",
  }),
);
```

```js
// MagixApp/index.js:106-138
passport.use(
  new Strategy(async function verify(username, password, cb) {
    const result = await db.query("SELECT * FROM users WHERE email = $1", [username]);
    if (result.rows.length === 0) {
      return cb(null, false); // no such user — don't leak which part failed
    }
    const user = result.rows[0];
    bcrypt.compare(password, user.password, (err, valid) => {
      if (err) return cb(err);
      if (valid) return cb(null, user); // password matched
      return cb(null, false); // wrong password
    });
  }),
);
```

`cb` is Passport's callback: `cb(err)`, `cb(null, user)` (success), `cb(null, false)` (failure).

## Guarding a route

```js
// MagixApp/index.js:55-62
app.get("/secrets", (req, res) => {
  if (req.isAuthenticated()) {
    res.render("secrets.ejs");
  } else {
    res.redirect("/login");
  }
});
```

## Logout

```js
// MagixApp/index.js:46-53
app.get("/logout", (req, res, next) => {
  req.logout(function (err) {
    if (err) return next(err);
    res.redirect("/");
  });
});
```

Note: the handler needs `next` in its signature for `next(err)` to work.

## Serialize / deserialize

Controls what goes into the session cookie store.

```js
// MagixApp/index.js:140-145
passport.serializeUser((user, cb) => {
  cb(null, user);
});
passport.deserializeUser((user, cb) => {
  cb(null, user);
});
```

- `serializeUser` runs after login: decides what to store (here the whole user object; production apps usually store just `user.id`).
- `deserializeUser` runs on every request with a session cookie: turns the stored data back into `req.user`.

![[Pasted image 20260927194739.png]]