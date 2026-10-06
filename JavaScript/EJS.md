Embedded JS

#### EJS with Express

1.MagixApp\index.js

```js
import express from "express";
const app = express();
const port = 3000;
app.get("/", (req, res) => {
    res.render("index.ejs", {
        dayType: "a weekday",
        advice: "its time to work hard",
    });
});
app.listen(port, () => {
    console.log(`Server Running on ${port}`);
});
```

1. MagixApp\views\index.ejs

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>WeekDay Warrior</title>
    </head>
    <body>
        Hey its <%= dayType %> , <%= advice %>
    </body>
</html>

```

1. Output

```bash
S Bala@LAPTOP-E79PK7CU MINGW64 ~/Documents/CodeSource/node-projects/MagixApp
$ curl localhost:3000
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>WeekDay Warrior</title>
    </head>
    <body>
        Hey its a weekday , its time to work hard
    </body>
</html>
```

#### EJS Tags

1. `<%= variable %>` - Escaped output (safe, HTML tags shown as text)
2. `<% console.log("Hello") %>` - Execute JS, no output
3. `<%- variable %>` - Unescaped output (renders HTML)
4. `<%% %%>` - Outputs literal `<%` - useful for docs
5. `<%# This is a comment %>` - Comment, no output
6. `<%- include("footer.ejs") %>` - Include another EJS file (note the spaces)

`<%=` vs `<%-` in MagixApp\views\index.ejs:

```html
<!-- data.htmlContent = "<strong>This is some strong text</strong>" -->
<p><%= data.htmlContent %></p>
<!-- outputs as text: <strong>This is some strong text</strong> -->

<p><%- data.htmlContent %></p>
<!-- renders as bold: This is some strong text -->
```

Lets write some JS inside EJS

```js
    const bowl = ["Apples", "Oranges", "Pears"];
    res.render("index.ejs", { fruits: bowl });
```

```html
<body>
  <% for(let i =0;i<fruits.length;i++){%>
      <li>
          <%= fruits[i] %>
      </li>
      <% } %>
</body>
```

#### Locals

- EJS keeps all variables passed to `res.render()` in an object called `locals`.
- TLDR: `locals` lets you safely check if a variable was passed before using it.

```js
// MagixApp\index.js
res.render("index.ejs", { data: randomObjects });
```

```html
<!-- MagixApp\views\index.ejs -->
<h1><%= data.title %></h1> <!-- direct access, throws if data is missing -->
<h1><%= locals.data.title %></h1> <!-- same value, via locals object -->

<% if (locals.data) { %>
    <p>Data exists</p>
<% } %>
```