1. So there are number of operations we can do to manipulate the DOM.

#### Access DOM Elements

1. Document: `getElementsByTagName()`
	1. https://developer.mozilla.org/en-US/docs/Web/API/Document/getElementsByTagName
	2. `document.getElementsByTagName("p");` -> HTMLCollection of elements with the given tag name
2. Document: `getElementsByClassName()`
	1. https://developer.mozilla.org/en-US/docs/Web/API/Document/getElementsByClassName
	2. `document.getElementsByClassName("test");` -> HTMLCollection of elements with the given class
3. Document: `getElementById()`
	1. https://developer.mozilla.org/en-US/docs/Web/API/Document/getElementById
	2. `document.getElementById("para");` -> Only return 1 single element
4. Document: `querySelector()`
	1. https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelector
	2. `querySelector(selectors)` -> A string containing one or more selectors to match. This string must be a valid CSS selector string
		1. CSS Selectors -> Just how we use CSS Selectors
		2. `document.querySelector("li a")` -> Select the anchor tag inside the list.
		3. If a select matches more than 1 item -> We only get back the first item in the document
		4. If we wanted all we need to all -> `querySelectorAll(selectors)`
#### Manipulate HTML Elements with JS

1. CSS Style
	1. https://www.w3schools.com/jsref/dom_obj_style.asp
	2. Lets say we want to change the font color of H1 -> `document.querySelector("h1").style.color = "Blue"`
	3. Lets say we want to change the font size of H1 -> `document.getElementById("demo").style.fontSize = "x-large";`
	4. The above documentation will help out here. Just put `style` first and rest you can look up above.
	5. Note : When we write CSS we do `font-size: xx-small;` so here in JS we are just doing camel case -> `fontSize : "xx-small"`
2. HTML Text
	1. Element: innerHTML
		1. https://developer.mozilla.org/en-US/docs/Web/API/Element/innerHTML
		2. `document.querySelector("button").innerHTML = "CLICK"`
		3. innerHTML should not be used to update `text` values rather HTML content
	2. Node: textContent
		1. https://developer.mozilla.org/en-US/docs/Web/API/Node/textContent
		2. `document.querySelector("button").textContent = "CLICK ME"`
	3. Note : InnerHTML actually gives the HTML source inside the element tag including other HTML tags. So something like `<p><strong>Hello There</strong></p>` is present. Inner HTML will get us -> `document.querySelector("p").innerHTML` -> `'<strong>Hello There</strong>'`. Where as `document.querySelector("p").textContent` -> `'Hello There'` as output.
3. HTML Attributes
	1. Lets say an anchor tag has `href` of google -> `document.querySelector("a").getAttribute("href")` return `'https://www.google.com'`
	2. We can modify it using -> `document.querySelector("a").setAttribute("href","https://www.bing.com/?cc=in")`. Now clicking on the link actually navigates us to google.


#### Separation of Concern

Lets say we want to hide a button based on user action. The best way to achieve this is by creating a class and then adding it or removing it from our HTML Element DOM rather than directly modifying CSS.

```js
document.querySelector("button").classList.add("invisible") // We can use this for animation as well.
```

```css
.invisible { // CSS Class
    display: none;
}
```

Well there is also toggle : https://developer.mozilla.org/en-US/docs/Web/API/DOMTokenList/toggle

#### Event Listeners ( Using `this` and `events` )

1. Sets up a function that will be called whenever the specified event is delivered to the target https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener
2. All event types : https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Events
3. Click Event : https://developer.mozilla.org/en-US/docs/Web/API/Element/click_event

```js
document.querySelector("button").addEventListener("click",handleClick)
function handleClick(){
    alert("I got Clicked")
}
```

1. For the first line we are giving the reference of the function -> `handleClick` instead of  `handleClick()` If we add the parenthesis -> we end up calling the function
#### The `this` keyword

Usage of `this` keyword refers to the HTML element but be careful. Note : Function vs Arrow Function usage of this ->  [[JavaScript#Perils of 'this' in JS]]

#### Sounds

```js
const wAudio = new Audio("./sounds/crash.mp3");
wAudio.play();
```

#### The 'event' object and Key Press

So whenever we trigger can event -> We can access the event using a parameter. It can be anything but normally its 'e' or 'event'. So if we press a button or key on the keyboard.

```js
button.addEventListener("click", function (e){ console.log(e)}) // PointerEvent {isTrusted: true, pointerId: 1, width: 1, height: 1, pressure: 0, …}
document.addEventListener("keydown", function (e){ console.log(e)}) // KeyboardEvent {isTrusted: true, key: 'w', code: 'KeyW', location: 0, ctrlKey: false, …}
document.addEventListener("keydown", function (e) {
    console.log(e.key); // a,w.s ..... Do Something meaningfull 
```

1. https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent

