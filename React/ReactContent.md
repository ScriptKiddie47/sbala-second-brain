# React Content

## Covered

- [[React Basics]]
    - Conditional rendering (ternary and `&&`)
    - JSX expressions
    - CSS: `className` and inline `style`
    - `map` and keys
- [[React Props]]
    - Basic props, passing objects, destructuring props
- [[React Hooks]]
    - Manual re-rendering vs. `useState`
    - `useState` basics
    - "Too many re-renders" and passing functions vs. calling them
- [[React Forms]]
    - Controlled components
    - `onChange`, `onSubmit`, `preventDefault()`
- [[React Event Handling]]
    - Mouse events (`onMouseEnter`, `onMouseLeave`)
- [[React Complex State]]
    - Stateful objects and updater functions
    - Passing functions in props (parent owns state, child triggers changes)
- [[React Config]]
    - Self-hosting fonts with `@font-face`
    - Common font gotchas

## Pending

1. **Immutable updates**: updating or removing one item in an object or array without mutating it. Practice a "toggle one item by id" pattern with `map`.
2. **Lifting state up and `children`**: deciding which component owns state, and passing components as `children` for layout.
3. **`useRef`**: keeping a value across renders without re-rendering, and focusing or measuring DOM elements.
4. **`useContext` and `useReducer`**: sharing state without prop drilling, and handling more complex state transitions.
5. **Custom hooks**: moving reusable stateful logic into its own function, such as `useLocalStorage`.
6. **Async data**: loading, error, and empty states around fetch calls.
7. **Rendering rules and StrictMode**: components should be pure, which explains doubled logs in development. Also `useMemo`, `useCallback`, and `React.memo`, once there is a performance problem.
8. **Routing**: `react-router` for multiple pages. Later: forms libraries and testing.
