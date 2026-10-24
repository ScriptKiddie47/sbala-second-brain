# React Hooks

1. React needs to re-render any change that is visible on the UI say a counter like below. We can do this manually like below

```jsx
const root = createRoot(document.getElementById('root'));
var count = 0;
function increase() {
    count++;
    root.render(
        <div className="container">
            <h1>{count}</h1>
            <button onClick={increase}>+</button>
        </div>
    );
}
root.render(
    <div className="container">
        <h1>{count}</h1>
        <button onClick={increase}>+</button>
    </div>
);
```

1. However this is way inefficient. So we use Hooks
2. React Hooks only work with Functional Components

## USE STATE

1. Note : Refer Destructuring before learning to use react state - [[JS#DESTRUCTURING]]


```jsx
import { useState } from 'react';
function App() {
    const [count, setCount] = useState(0);
    console.log(count);
    function increase() {
        setCount(count + 1);
    }
    function decrease() {
        setCount(count - 1);
    }
    return (
        <div className="container">
            <h1>{count}</h1>
            <button onClick={increase}>+</button>
            <button onClick={decrease}>-</button>
        </div>
    );
}
export default App;
```

1. Things to Note : https://react.dev/reference/react/useState#caveats

## WHY CALLING A SETTER IN JSX CAUSES "Too many re-renders"

1. The classic bug:

```jsx
const [time, setTime] = useState(new Date().toLocaleTimeString());
return (
    <button onClick={setTime(new Date().toLocaleTimeString())}>
        GET TIME
    </button>
);
```

1. This throws `Uncaught Error: Too many re-renders. React limits the number of renders to prevent an infinite loop.`
2. The fix:

```jsx
<button onClick={() => setTime(new Date().toLocaleTimeString())}>
    GET TIME
</button>
```

1. The root cause : **JSX is just JavaScript**. Everything inside `{}` is an *expression that is evaluated during render*, not later when the user clicks.
2. So `onClick={setTime(...)}` means: "during render, immediately call `setTime(...)`, and pass its return value (`undefined`) to `onClick`".
3. `setTime` is a state setter. Calling it during render triggers a re-render.
4. Re-render runs the function component again → hits the same line → calls `setTime` again → schedules another re-render → **infinite loop**.
5. React detects this (after ~25 nested renders) and throws to protect the browser from freezing.

1. What `onClick` actually expects : a **function reference** (a handler) that React will call *later*, when the click event fires.
2. Two valid ways to pass it:
    1. Named handler : `function handleClick() { setTime(...); }` then `onClick={handleClick}` — note: no parentheses.
    2. Inline arrow : `onClick={() => setTime(...)}` — here the arrow function itself is the reference; `setTime` is called only when the arrow runs, i.e. on click.
3. Rule of thumb : `onClick={fn}` = "call `fn` on click". `onClick={fn()}` = "call `fn` now and use its result as the handler".

1. The arrow function **defers** execution. React runs its body only when the event occurs, so no state update happens during render.
2. The render → event → render cycle:
    1. Render : React runs `<App />`, evaluates JSX, produces elements. No state updates are allowed here.
    2. Commit : React writes the DOM. The `onClick` handler is attached.
    3. Event (user clicks) : React calls the handler → `setTime` runs → React schedules a re-render → back to step 1.
3. Passing `fn()` short-circuits this by doing the "event" step *during* step 1.

1. Related gotchas :
    1. Same trap with other props that expect functions : `onChange`, `onSubmit`, `array.map(fn)` vs `array.map(fn())`.
    2. `setCount(count + 1)` is fine *inside* a handler, but if you ever call it directly in the component body you get the same loop.
    3. Updater form `setCount(prev => prev + 1)` is preferred when the new state depends on the previous one, to avoid stale closures.
2. Quick check to remember : *"Am I passing the function, or am I calling it?"* If you see `()` after the handler name in JSX, wrap it in `() => { ... }`.

## USE EFFECT()

1. https://www.youtube.com/watch?v=-4XpG5_Lj_o
2. Used to control side effects. A side effect is anything that reaches outside the component: fetching data, timers, subscriptions, or `localStorage`.
3. Rule of thumb: use an event handler for work triggered by a user action. Use `useEffect` to sync the component with something outside React.

```jsx
    useEffect(() => {
        // The code that we want to run
        // Optional : Return Function
    }, []); // The Dependency Array
```

1. Lets say we have simple counter app and we want something to happen when count changes.
2. The effect runs after the first render, and again whenever a dependency changes.
3. The dependency array controls when it runs:
    1. No array : runs after every render.
    2. `[]` : runs only on mount (once).
    3. `[count]` : runs on mount, and again whenever `count` changes.
4. The optional returned function is the cleanup. It runs before the effect runs again, and when the component unmounts.

```jsx
import { useState, useEffect } from 'react';

function App() {
    const [count, setCount] = useState(0);
    useEffect(() => {
        console.log(`Count : ${count}`);
        return () => {
            console.log(`I am being cleaned up!`); // Optional Function
        };
    }, [count]);

    return (
        <div>
            <h1>Count: {count}</h1>
            <button onClick={() => setCount(count + 1)}>Increment</button>
            <br />
            <br />
            <button onClick={() => setCount(count - 1)}>Decrement</button>
        </div>
    );
}
export default App;
```

1. Example: saving the count to `localStorage` every time it changes:

```jsx
useEffect(() => {
    localStorage.setItem('count', count);
}, [count]);
```


## USE REF

1. `useRef` is a React Hook that lets you keep a value between renders without re-rendering when it changes. It returns an object with a `current` property.
2. Rule of thumb: use **state** for anything that should appear on screen. Use a **ref** for values the component needs to remember but not display, like timer IDs, previous values, or DOM nodes.

```jsx
import { useState, useRef } from 'react';
function App() {
    const [count, setCount] = useState(0);
    const countRef = useRef(0);
    function handleIncrement() {
        setCount(count + 1);
        countRef.current++;

        console.log(`State: ${count}`); // 0
        console.log(`Ref: ${countRef.current}`); // 1
    }
    return (
        <div>
            Count: {count}
            <button onClick={handleIncrement}>Increment</button>
        </div>
    );
}
export default App;
```

1. Changing `countRef.current` does not re-render the component, so the screen still shows the old count, even though the ref is now 1. Don't read `ref.current` during render, since the render may not reflect the latest value.
2. We see State as 0 because state updates apply on the next render, so `count` in this function is still 0. `countRef.current` changes right away.
3. `useRef(0)` uses the initial value only on the first render. Later renders return the same ref object.
4. Another common use is DOM focusing. The ref is only attached after the first render, so the focus call goes in `useEffect`, which runs after the DOM exists. The `[]` array makes it run once on mount.

```jsx
import { useEffect, useRef } from 'react';
function App() {
    const inputRef = useRef(null);
    useEffect(() => {
        inputRef.current.focus();
    }, []);
    return (
        <div>
            <input ref={inputRef} type="text" placeholder="Say something" />
        </div>
    );
}
export default App;
```

## USE CONTEXT

1. `useContext` is a React Hook that lets you read and subscribe to context from your component.
2. No need to deal with prop drilling
3. Create file called - src/context.js

```js
import { createContext } from 'react';
export const DashboardContext = createContext(null);
```

4. `App.jsx` provides the value. Any component inside the `Provider` can read it, no matter how deep.

```jsx
import Dashboard from './Dashboard';
import { DashboardContext } from './context';

function App() {
    const user = {
        isSubscribed: true,
        name: 'Syndicate',
    };
    return (
        <DashboardContext.Provider value={user}>
            <Dashboard />
        </DashboardContext.Provider>
    );
}
export default App;
```

5. `Dashboard.jsx` doesn't need to know about the user. It just passes the layout along.

```jsx
import Sidebar from './Sidebar';
import Profile from './Profile';

function Dashboard() {
    return (
        <div>
            <Sidebar />
            <Profile />
        </div>
    );
}
export default Dashboard;
```

6. `Sidebar.jsx` reads the value with `useContext`.

```jsx
import { useContext } from 'react';
import { DashboardContext } from './context';

function Sidebar() {
    const user = useContext(DashboardContext);
    return (
        <div>
            <p>Name : {user.name}</p>
            <p>Subscription Status : {user.isSubscribed ? 'True' : 'False'}</p>
        </div>
    );
}
export default Sidebar;
```

7. Notes:
    1. The `value` prop on `Provider` is what consumers receive. Components between `App` and `Sidebar` don't need a `user` prop.
    2. Components that call `useContext` re-render when the `value` changes.
    3. `createContext(null)` is the default, used only when a component has no `Provider` above it.
    4. Use context for data many components need, like the current user or theme. For one or two levels of props, passing props directly is simpler.