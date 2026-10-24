 This won't language features on a syntactical level. Better use documentation for this. This note mostly covers the code style , important concepts.

#### HIGHER ORDER FUNCTION

```js
function add(n1,n2){
    return n1 + n2;
}
function multiply(n1,n2){
    return n1*n2;
}
function calculator(n1,n2,operator){
    return operator(n1,n2);
}
console.log(calculator(5,5,add)); // 10
console.log(calculator(5,5,multiply)); //25
```

Higher order functions are functions that take one or more functions as arguments, or return a function as their result

#### Perils of 'this' in JS

Arrow functions don't have their own `this` — they inherit it from the enclosing scope, lexically, at the point where they're _defined_, not from how they're _called_.'

#### JSON

```js
const jsonData = JSON.stringify(data); // TO STRING
const data = JSON.parse(jsonData); // TO A JS OBJECT
```



#### REDUCE

1. https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce
2. It looks difficult but actually is quite straight forward

The callback receives `(accumulator, currentValue, index, array)`. Whatever you
return becomes the `accumulator` for the next iteration.

```js
var numbers = [3,56,2,48,5];
var newNumber = numbers.reduce(function(accumulator,currentNumber){
    console.log(`accumulator:${accumulator}`)
    console.log(`currentNumber:${currentNumber}`)
    return accumulator+currentNumber;
})
console.log(newNumber) // 114
```

With no initial value, the first element is used as the starting `accumulator`
and iteration begins at index 1. Pass an initial value as the second argument to
set your own starting point (and to avoid a `TypeError` on an empty array):

```js
numbers.reduce(function(acc, n){ return acc + n; }, 0);
```

#### DESTRUCTURING 

1. https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring
2. ES6
3. The **destructuring** syntax is a JavaScript syntax that makes it possible to unpack values from arrays, or properties from objects, into distinct variables
4. Each destructured property can have a default value. The default value is used when the property is not present, or has value undefined. Below 1 is default value.

```js
const arr = [10,20,30];
const [n1,n2,n3,n4=1] = arr;
console.log(`${n1}:${n2}:${n3}:${n4}`) // "10:20:30:1"
```

```js
const car = {
  age: 5,
  color: "red",
  brand: "Toyota"
} 
const {age,color,brand} = car;
console.log(`${age}:${color}:${brand}`) // "5:red:Toyota"
```

5. Renaming (aliasing) — object destructuring only. Use `key: newName` to bind a property to a differently named variable. Left of `:` is the property to read, right is the variable to create. Arrays match by position, so there is nothing to alias.

```js
const { age: carAge, color: carColor } = car;
console.log(`${carAge}:${carColor}`) // "5:red"

// defaults go on the variable side
const { age: carAge2 = 0, wheels = 4 } = car;
```

6. `key:` cannot appear inside `[...]` (arrays are positional, so there is no key to alias) — to reach nested values, destructure the element first, then destructure it, optionally nesting deeper in the same pattern:

```js
const cars = [ { model: 'Honda Civic', coloursByPopularity: ['black', 'silver'], speedStats: { topSpeed: 140, zeroToSixty: 8.5, }, }, { model: 'Tesla Model 3', coloursByPopularity: ['red', 'white'], speedStats: { topSpeed: 150, zeroToSixty: 3.2, }, }, ];
const [hondaCivic, tesla] = cars;
const {
    model,
    coloursByPopularity,
    speedStats: { topSpeed: teslaTopSpeed }, // nested object pattern
} = tesla;

console.log(`teslaTopSpeed : ${teslaTopSpeed}`);
```

1. Rest properties and rest elements
2. You can end a destructuring pattern with a rest property `...rest`. For array destructuring, it collects remaining elements of the iterable into a new array called rest (or any name you give it). For object destructuring, it copies all enumerable own properties of the object that are not already picked off by the destructuring pattern into a new object called `rest`.

```js
const { a, ...others } = { a: 1, b: 2, c: 3 };
console.log(others); // { b: 2, c: 3 }

const [first, ...others2] = [1, 2, 3];
console.log(others2); // [2, 3]
```

#### OBJECT SPREAD & COMPUTED PROPERTY NAMES

Not destructuring, despite the function name below — this is **object spread** combined with a **computed property name**. Together they build a *new* object instead of mutating the original.

```js
const car = {
    vehicleNumber: 100022,
    vehicleName: 'Suzuki',
    vehicleAge: 10,
};
function modifyObjectDataUsingDestructuring(car) {
    return { ...car, [`vehicleAge`]: 20 };
}
console.log(modifyObjectDataUsingDestructuring(car));
// { vehicleNumber: 100022, vehicleName: 'Suzuki', vehicleAge: 20 }
console.log(car.vehicleAge); // 10 (original untouched — a new object was returned)
```

1. `...car` copies the object's own enumerable properties into a new object (a *shallow* copy).
2. `[`vehicleAge`]` is a **computed property name** — the key is an expression, evaluated at runtime. It reads as "use the value of this expression as the key".
3. Properties are applied **left to right**, so a key placed *after* the spread overwrites (or adds to) the copy. Reverse the order to `{ [`vehicleAge`]: 20, ...car }` and the spread would win, silently discarding the update.
4. **Immutability:** `car` is never mutated. This "copy then override" pattern is exactly how React state is updated.

A bare key is a *literal* name — `{ name: value }` creates a property called `"name"`, not one named after the variable. The brackets are what make it dynamic, which is why one line can replace an entire `if/else` chain:

```js
setState((prev) => ({ ...prev, [name]: value }));
```

1. A new object is returned above. Similarly a new array is returned below

```js
const fruits = ['Oranges', 'Guava'];
const fruitsV2 = [...fruits, 'Apple']; // [ 'Oranges', 'Guava', 'Apple' ]
```

1. Good Read : https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array#copy_an_array
2. 