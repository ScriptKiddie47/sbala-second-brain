	# React Props

Props are read-only inputs passed from a parent to a child component.

## 1. Basic Props

```jsx
function Card(props) {
  console.log(props); // { name: "Beyonce" }
  console.log(props.name); // Beyonce
  return <h2>{props.name}</h2>;
}

function App() {
  return <Card name="Beyonce" />;
}
```

Output in JS console:

```js
{
    "name": "Beyonce"
}
Beyonce
```

## 2. Passing Objects as Props

```jsx
const contactList = [
  {
    name: 'Beyonce',
    number: '+123 456 789',
    email: 'b@beyonce.com',
    imageSrc:
      'https://blackhistorywall.files.wordpress.com/2010/02/picture-device-independent-bitmap-119.jpg',
    imageAlt: 'avatar_img',
  },
];

function Card(props) {
  return <h2>{props.contact.name}</h2>;
}

function App() {
  return <Card contact={contactList[0]} />;
}
```

## 3. Destructuring Props

```jsx
function Card({ name }) {
  return <h2>{name}</h2>;
}

// For objects:
function Card({ contact }) {
  return <h2>{contact.name}</h2>;
}
```

> Pitfall: props are read-only. Never do `props.name = "X"` inside the child. Derive new values instead: `const displayName = props.name.toUpperCase()`.
