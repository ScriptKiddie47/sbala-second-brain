
1. Work with Statefull Objects

```jsx
import { useState } from 'react';

function App() {
    const [fullName, setFullName] = useState({
        fName: '',
        lName: '',
    });
    function handleChange(event) {
        const newValue = event.target.value;
        const inputName = event.target.name;
        console.log(`Value: ${newValue} - Input name : ${inputName} `);
        setFullName((prev) => {
            if (inputName == 'fname') {
                return { fName: newValue, lName: prev.lName };
            } else if (inputName == 'lname') {
                return { fName: prev.fName, lName: newValue };
            }
        });
    }
    return (
        <div className="container">
            <h1>
                Hello {fullName.fName}
                {fullName.lName}
            </h1>
            <form>
                <input
                    onChange={handleChange}
                    name="fname"
                    placeholder="First Name"
                    value={fullName.fName}
                />
                <input
                    onChange={handleChange}
                    name="lname"
                    placeholder="Last Name"
                    value={fullName.lName}
                />
                <button type="submit">Submit</button>
            </form>
        </div>
    );
}
export default App;
```

1. Warning  -> Never access Events in Statefull Setter.
2. A bit cleaner way to do the above


```jsx
    function handleChange(event) {
        const { value, name } = event.target;

        console.log(`Value: ${value} - Input name : ${name} `);
        setFullName((prev) => {
            if (name == 'fname') {
                return { fName: value, lName: prev.lName };
            } else if (name == 'lname') {
                return { fName: prev.fName, lName: value };
            }
        });
    }
```

1. We can do one level deeper

```jsx
   function handleChange(event) {
        const { value, name } = event.target;
        setFullName((prev) => ({ ...prev, [name]: value }));
    }
```

1. Refer [[JS#OBJECT SPREAD & COMPUTED PROPERTY NAMES]]

### MANAGE COMPONENT TREE - PASSING FUNCTIONS IN PROPS

The list state lives in the parent (`App`). A child can't change it directly, so the parent passes a function down through props and the child calls it.

```jsx
function deleteItem(index) {
    setTodosList((prev) => prev.filter((_, i) => i !== index)); // Keep every item except the one at `index`; `_` is the unused item value.
}

{todosList.map((item, index) => (
    <TodoItem key={index} id={index} item={item} onDelete={deleteItem} />
))}
```

```jsx
function TodoItem({ id, item, onDelete }) {
    return <li onClick={() => onDelete(id)}>{item}</li>;
}

export default TodoItem;
```

1. The parent owns the state. The child only calls `onDelete`, which runs `deleteItem` in the parent.
2. `filter` returns a new array without the matching item, so React re-renders the list.
3. Using `index` as the key works for now, but it breaks when items are removed from the middle of the list. Use a unique id instead.
