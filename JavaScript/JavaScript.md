This won't language features on a syntactical level. Better use documentation for this. This note mostly covers the code style , important concepts.

#### Higher Order Function

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

#### Objects

