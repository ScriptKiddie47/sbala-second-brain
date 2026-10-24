
#### Callbacks

1. A callback function is a function passed into another function as an argument, which is then invoked inside the outer function to complete some kind of routine or action. A node example below

```js
setTimeout(() => {
    console.log(`1`);
}, 1000);
```

1. HTML Event call back

```js
document.querySelector("button").addEventListener("click",handleClick)
function handleClick(){
    alert("I got Clicked")
}
```

#### Error First Callback

```js
import fs from 'fs';
fs.readFile('./test.txt', { encoding: 'utf-8' }, (err, data) => {
    if (err) {
        console.warn(data);
    } else {
        console.log(data);
    }
});
```

#### Custom Promise

1. Create a function that accepts both a resolve and reject - `const firstPromise = new Promise((resolve, reject) => {});`

```js
const firstPromise = new Promise((resolve, reject) => {
    const rand = Math.floor(Math.random() * 2);
    if (rand === 0) {
        resolve();
    } else {
        reject();
    }
});

firstPromise
    .then(() => console.log(`Success`))
    .catch(() => console.error(`Something went wrong`));
```

#### FS using Promise

```js
import fs from 'fs';
fs.promises
    .readFile('./test.txt', { encoding: 'utf-8' })
    .then((data) => console.log(data))
    .catch((err) => console.error(err));
```

1. Notice the usage of `promise` keyword above.

#### Fetch Using Promise 

```js
fetch(`https://pokeapi.co/api/v2/pokemon/ditto`)
    .then((res) =>
        res
            .json()
            .then((data) => console.log(data))
            .catch((err) => console.error(err))
    )
    .catch((err) => console.error(err));

```

#### Evolution of Promise to Async Await

1. We must mark a function as async if we want to use Async Await functionality.
2. FS using `await`

```js
async function loadFile() {
    try {
        const data = await fs.promises.readFile('./test.txt', {
            encoding: 'utf-8'
        });
        console.log(data);
    } catch (err) {
        console.error(err);
    }
}
loadFile();
```

1. Fetch using `async await` - Without Error Handling

```js
async function fetchPokemon() {
    const res = await fetch(`https://pokeapi.co/api/v2/pokemon/ditto`);
    const data = await res.json();
    console.log(data);
}
fetchPokemon();
```