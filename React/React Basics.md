# React Basics

## 1. Conditional Rendering

Use ternary for if/else:

```jsx
<div>
  {isLoggedIn ? (
    <AdminPanel />
  ) : (
    <LoginForm />
  )}
</div>
```

When you don't need the `else` branch, use shorter [logical `&&`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND#short-circuit_evaluation):

```jsx
{isLoggedIn && <AdminPanel />}
```

> Pitfall: `{count && <X />}` renders `0` when `count` is `0`. Use `{count > 0 && <X />}` instead.

## 2. JSX Expressions

Only JS **expressions** (not statements like `if` / `for` / `const`) can go inside curly braces:

```jsx
<li>Year is {new Date().getFullYear()}</li>
```

#### String Interpolation in JSX

```jsx
const fname = "Shrutosom";
const lname = "Bala";
<li>{`${fname} ${lname}`}</li>
```

## 3. Working with CSS (class Name & Attributes)

```css
.master {
  font-family: "Slabo 27px", serif;
  font-weight: 400;
  font-style: normal;
}
```

```jsx
import "./App.css";

<div className="master">
  {/* ... */}
</div>
```

Attributes are camelCase in JSX. For example [`contenteditable`](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Global_attributes/contenteditable) becomes `contentEditable`:

```jsx
<h1 contentEditable="true">This ought to be Fun</h1>
```

Note: `class` becomes `className`.

### Inline CSS

First `{}` enters JS, second `{}` is the style object:

```jsx
<li style={{ color: "red" }}>Lucky Number is {luckyNumber}</li>
```

Or with a variable:

```jsx
const customStyle = {
  color: "red",
  fontSize: "20px",
};

<li style={customStyle}>Lucky Number is {luckyNumber}</li>
```

> Style keys are camelCase (`fontSize`, `backgroundColor`). Values are strings, except unitless numbers like `lineHeight: 1.5`.

#### REACT MAPS

NOTE: MAP EXPECTS A FUNCTION NOT A VALUE. IT FOLLOWS THE ES6 STANDARDS. SO SINGLE STATEMENT IS RETURNED WITHOUT 'RETURN' KEYWORD USE. FOR MULTILINE USE RETURN

`Array.prototype.map` runs a function on every element and returns a **new array** of the results — it does not change the original array. It always returns the same number of items as it started with:

```js
const nums = [1, 2, 3];
const doubled = nums.map((n) => n * 2); // [2, 4, 6]
```

The callback receives three arguments: `(element, index, array)`. In React you usually only need the element, but the index is handy when there is no unique id:

```jsx
{items.map((item, index) => (
  <li key={index}>{item}</li>
))}
```

> Prefer a stable id over the array index. Indexes break as keys when the list is reordered, inserted into, or filtered.

`map` turns an array into an array of JSX elements. Given this data:

```js
const contacts = [
  {
    id: 1,
    name: "Beyonce",
    imgURL: "https://example.com/beyonce.jpg",
    phone: "+123 456 789",
    email: "b@beyonce.com",
  },
  // ...
];
```

Render a `Card` for each contact:

```jsx
function Card(props) {
    return (
        <div className="card">
            <div className="top">
                <h2 className="name">{props.contact.name}</h2>
                <Avatar imageURL={props.contact.imgURL} />
            </div>
            <div className="bottom">
                <p className="info">{props.contact.phone}</p>
                <p className="info">{props.contact.email}</p>
            </div>
        </div>
    );
}

function Avatar({ imageURL }) {
    return <img className="circle-img" src={imageURL} />;
}

function App() {
    return (
        <div className="master">
            <h1 className="heading">My Contacts</h1>
            {contacts.map((c) => (
                <Card key={c.id} contact={c} />
            ))}
        </div>
    );
}
```

> The `key` prop must go on the element returned from `map` — here `<Card>` — not on a child inside `Card`. React looks for `key` on the siblings React reconciles, so a key on an inner element (e.g. the `<div className="card">`) is ignored and warns: *"Each child in a list should have a unique key prop."*

Use a stable, unique value (like `c.id`) for the key. `key` is a React-only attribute and is not passed to the component as a prop.

> `key` is reserved by React: it is used only for list reconciliation and is **not** passed to the component, so `props.key` is always `undefined` (and reading it triggers a dev warning). If the child needs that value, pass it as a normal prop:

```jsx
<Card key={c.id} id={c.id} contact={c} />
```

```jsx
function Card(props) {
  return <p>{props.id}</p>; // not props.key
}
```
