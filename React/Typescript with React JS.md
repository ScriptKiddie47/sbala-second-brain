
## Props with TS

```ts
// File - bluepine-ts/src/types/BluePine.ts
interface Person {
  name: string;
  age: number;
  isMarried: boolean;
}
export { type Person };
```

```tsx
import type { Person } from './types/BluePine';
function PersonCard(person: Person) {
    return (
        <div>
            <p>Name: {person.name}</p>
            <p>Age: {person.age}</p>
            <p>This person{person.isMarried ? 'is married' : 'is single'}</p>
        </div>
    );
}
export default PersonCard;
```

```tsx
import './App.css';
import PersonCard from './PersonCard';
function App() {
    return (
        <>
            <PersonCard name={'Pedro'} age={20} isMarried={false} />
            <PersonCard name={'Pedro'} age={25} isMarried={true} />
        </>
    );
}
export default App;
```

#### Event Types

```tsx
 function handleChange(event: React.ChangeEvent<HTMLInputElement>){
        setPersonBio(event.target.value);
    }
function handleSubmit(event: React.SubmitEvent<HTMLFormElement>) {
        event.preventDefault();
    }
```

