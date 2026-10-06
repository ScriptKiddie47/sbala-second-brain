# CSS Grid

### Source - [The Complete Full-Stack Web Development Bootcamp](https://www.udemy.com/course/the-complete-web-development-bootcamp/) 

1. Grid Sizing : https://appbrewery.github.io/grid-sizing/
	1. This is a bit hard so its best to refer the Udemy tutorial video.
	2. Just remember its just rows and cols. That's all.
	3. repeat() is fun & time saving, grid-auto-rows is fun
2. Grid Placement : 
	1. Few terminologies -> Grid Container, Rows & Cols Tracks
	2. `grid-column: span 2` -> Fancy, Fun, Useful but need to revisit.

# HTML Forms - Label vs Input

1. `<label>` = description text. No data, just tells user what to fill. Clicking it focuses its input. Important for accessibility.
2. `<input>` = the control that collects data and gets submitted (`value`).

Link with `for` / `id`. Submit key comes from `name`, not `id`:

```html
<!-- MagixApp\views\index.ejs -->
<form action="/submit" method="POST">
  <label for="fName">First name:</label>
  <input type="text" id="fName" name="fName" placeholder="First name">
  <label for="lName">Last name:</label>
  <input type="text" id="lName" name="lName" placeholder="Last name">
  <input type="submit" value="OK">
</form>
```

- `for="fName"` matches `id="fName"` -> clicking label focuses input
- `name="fName"` -> key sent on submit, e.g. `req.body = { fName: "John", lName: "Doe" }`

#### How to add a font

1. Get code from Google Fonts and just inject baby.