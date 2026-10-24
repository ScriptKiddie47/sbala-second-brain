# TypeScript

## Setup

1. Install globally on your computer: `npm install -g typescript`
2. Verify the installation: `tsc -v`
3. Create a TypeScript config file at the project root: `tsc --init`
   - This generates `tsconfig.json`. Turn on `"strict": true` to get the full benefit of type checking.

> **Note:** TypeScript's linting rules and style guides discourage wrapper types like `Number`, `String`, and `Boolean`. Use `number`, `string`, and `boolean` instead.

## Simple Types

```ts
let id = 5;
let company: string = "Acme Corp";
let isPublished: boolean = true;
let ids: number[] = [1, 2, 3, 4, 5];
let x: any = "pedro"; // Anything goes, but type checking is turned off
let y: unknown = "pedro"; // Anything goes, but you must narrow it before use
let xArr: any[] = ["Pedro", 0, true];

const concatenateValues = (a: string, b: string): string => {
  return a + b;
};
console.log(concatenateValues("Hello", "World"));
```

- `let id = 5;` needs no annotation because TypeScript infers `number` from the value.
- Prefer `unknown` over `any`. `any` disables type checking, while `unknown` forces you to check the type before using the value.

## Interface

```ts
interface UserInterface {
  id: number;
  name: string;
  age?: number; // Optional
  greet(message: string): void;
}

const sUser: UserInterface = {
  id: 1,
  name: "S",
  greet(message: string): void {
    console.log(message);
  },
};
```

## Type vs Interface

Both can describe object shapes. Use `interface` for object shapes that may be extended or merged. Use `type` for unions, intersections, and aliases for primitives.

```ts
type ID = string | number; // type can express unions
interface Person { name: string }
interface Student extends Person { school: string } // interface can extend
```

## Union and Intersection

```ts
// Union: a value that is one of several types
type IDFieldType = string | number;
const printId = (id: IDFieldType): void => {
  console.log("ID: " + id);
};

// Intersection: a value that has all of the listed shapes
interface BusinessPartner {
  name: string;
  creditScore: number;
}
interface UserIdentity {
  id: number;
  email: string;
}
type Contractor = BusinessPartner & UserIdentity;

const signContract = (contractor: Contractor): void => {
  console.log(
    `Contract signed by ${contractor.name} with email: ${contractor.email}`,
  );
};

signContract({
  name: "pedro",
  creditScore: 800,
  id: 1,
  email: "pedro@gmail.com",
});
```

## Enums

```ts
enum LoginError {
  Unauthorized = "unauthorized",
  NoUser = "No User",
  WrongCredentials = "wrongcredentials",
  Internal = "internal",
}

const printErrorMsg = (error: LoginError): void => {
  if (error === LoginError.Unauthorized) {
    console.log("User not authorized");
  } else if (error === LoginError.NoUser) {
    console.log("No user found");
  }
};

printErrorMsg(LoginError.Unauthorized);
```

> **Tip:** Many teams prefer a string literal union instead, because it needs no runtime code:
>
> ```ts
> type LoginError = "unauthorized" | "No User" | "wrongcredentials" | "internal";
> ```

## Generics

```ts
class StorageContainer<T> {
  private contents: T[];

  constructor() {
    this.contents = [];
  }

  addItem(item: T): void {
    this.contents.push(item);
  }

  getItem(idx: number): T | undefined {
    return this.contents[idx];
  }
}

const userName = new StorageContainer<string>();
userName.addItem("Apple");
userName.addItem("Oranges");
console.log(userName.getItem(0)); // Apple

const userIds = new StorageContainer<number>();
userIds.addItem(101);
userIds.addItem(102);
console.log(userIds.getItem(1)); // 102
```

## Read Only

`readonly` prevents a property from being reassigned after the object is created.

```ts
interface Employee {
  readonly employeeId: number;
  name: string;
}

const emp: Employee = { employeeId: 7, name: "Ana" };
emp.name = "Ana Maria"; // OK
emp.employeeId = 8;     // Error: Cannot assign to 'employeeId' because it is a read-only property
```

# Modules and Exports

A file with a top-level `import` or `export` is a module. Its declarations stay private unless exported.

### Named Exports

```ts
// math.ts
export interface Props { name: string }
export const greeting = "hi";
export function add(a: number, b: number) { return a + b; }

// or list them at the end
const total = 10;
export { total as sum };
```

```ts
import { add, greeting, type Props } from "./math";
import { add as plus } from "./math"; // rename on import
```

### Default Export

One per module. The importer picks the name.

```ts
// Person.tsx
export default function Person() { /* ... */ }
```

```ts
import Person from "./Person";
```

### Type-Only Exports

Types are erased at compile time. Use `export type` and `import type`.

```ts
export type Props = { name: string };
```

```ts
import type { Props } from "./types/Props";
```

With `verbatimModuleSyntax` enabled, mixed imports need `type` on the specifier: `import { greeting, type Props } from "./math";`.

### Re-Exports

Useful for barrel files like `index.ts`:

```ts
export { Person } from "./Person";
export { default as Card } from "./Card";   // default export exposed as a named one
export * from "./types/Props";               // all named exports, no default
export type { Props } from "./types/Props";
```

### Namespace and Side-Effect Imports

```ts
import * as math from "./math";   // math.add(...), math.greeting
import "./styles.css";            // runs the module, imports nothing
```

> **Note:** A bare `import "./file"` does not bring any names into scope. If a type seems "not found," make sure it is exported and imported by name.

> **Note:** `module.exports` and `exports.x` are CommonJS syntax, not ES modules. `export = ...` is a TypeScript-only form for CommonJS interop.

# Optional Values and `?`

The `?` symbol has three related meanings in TypeScript.

### Optional Property

A property marked with `?` may be missing from an object. Its type includes `undefined`.

```ts
interface Task {
  id?: number;        // same as: id: number | undefined (but may be omitted)
  description: string;
}

const a: Task = { description: "Write docs" };        // OK, id omitted
const b: Task = { id: 1, description: "Write docs" }; // OK
```

Reading an optional property gives `number | undefined`, so TypeScript requires a check before using it as a number:

```ts
const doubled = a.id * 2;        // Error: 'a.id' is possibly 'undefined'
const safe = (a.id ?? 0) * 2;    // OK: fall back to 0
```

### Optional Parameter

A parameter marked with `?` may be omitted when calling the function. Inside the function it has type `T | undefined`.

```ts
function greet(name?: string): string {
  return name ? `Hello, ${name}` : "Hello";
}

greet();        // "Hello"
greet("Ada");   // "Hello, Ada"
```

Optional parameters must come after required ones.

### Optional Chaining `?.`

This is a runtime check, not a type declaration. If the value before `?.` is `null` or `undefined`, the whole expression returns `undefined` instead of throwing.

```ts
const user: { profile?: { city: string } } = {};

console.log(user.profile?.city);          // undefined, no error
console.log(user.profile?.city ?? "N/A"); // "N/A"
```

### `?` and `| undefined`

`?` on a property is shorthand for adding `| undefined` to its type, with one difference: a property with `?` can be left out of the object entirely.

```ts
interface A { id?: number }             // id may be missing
interface B { id: number | undefined }  // id must be present, but may be undefined

const x: A = {};                        // OK
const y: B = {};                        // Error: Property 'id' is missing
const z: B = { id: undefined };         // OK
```

The same applies to React state. `useState<string>()` has type `string | undefined` because no initial value is given.

> **Note:** With `"strict": true`, spreading a possibly-`undefined` value can produce an object whose required fields are missing. Provide a full starting value, or guard the update, as in `setTask((prev) => (prev ? { ...prev, description: text } : prev))`.
