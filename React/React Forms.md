#### CONTROLLED COMPONENT + EVENTS

```jsx
import { useState } from 'react';
function App() {
    const [name, setName] = useState('');
    const [headingText, setHeadingText] = useState('');
    function handleClick(e) {
        setHeadingText(name);
        e.preventDefault(); // Prevents the default behaviour of Refreshing the page.
    }
    function handleOnChange(event) {
        setName(event.target.value);
    }
    return (
        <div className="container">
            <h1>{headingText}</h1>
            <form onSubmit={handleClick}>
                <input
                    onChange={handleOnChange}
                    type="text"
                    placeholder="What's your name"
                    value={name}
                ></input>
                <button type="submit">Submit</button>
            </form>
        </div>
    );
}
export default App;

```

1. Well this is a fun way to learn about `onChange` events ( Different than `onchange` DOM manipulation using `document.querySelector()`)
2. Things to note here is REACT IS CONTROLLING EVERYTHING ABOUT THE FORM STATE. NOT DOM MANIPULATION
3. [Controlling an input with a state variable](https://react.dev/reference/react-dom/components/input#controlling-an-input-with-a-state-variable)
4. `preventDefault()` -> This quite usefull. Generally the page reloads if a form is submitted. We prevent that using this 
