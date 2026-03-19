

# 📘 MODULE 3 — State & Data Flow (Complete Deep Notes)

This module transforms React from “static UI rendering” into a dynamic, interactive system.

We will cover:

1. What is State? (Formal definition)
2. useState Hook (Deep understanding)
3. State vs Props (Architectural clarity)
4. React Rendering & State Updates
5. Functional Updates
6. Batching & Asynchronous Nature of State
7. Controlled vs Uncontrolled Components
8. Forms in React (Detailed)
9. Lifting State Up
10. Derived State & Its Pitfalls
11. State Colocation Principle
12. Immutable Update Patterns
13. Common State Mistakes
14. Practical Integrated Examples
15. Optional Exercises

No shortcuts. Full clarity.

---

# 3.1 — What is State?

## Definition

State is a built-in React mechanism that allows a component to store, manage, and update dynamic data that affects rendering.

Unlike props, state is:

* Managed inside the component
* Mutable (via setter function)
* Causes re-render when updated

---

## Conceptual Model

React UI is a function of:

```
UI = f(props, state)
```

When state changes:

* Component re-renders
* Virtual DOM updates
* Real DOM updates (minimally)

---

## Example: Static vs Dynamic

Static:

```jsx
<p>0</p>
```

Dynamic with state:

```jsx
const [count, setCount] = useState(0);
<p>{count}</p>
```

Now UI reacts to state changes.

---

# 3.2 — useState Hook

## Definition

useState is a React Hook that allows functional components to declare and manage state variables.

---

## Syntax

```jsx
const [state, setState] = useState(initialValue);
```

Where:

* state → current value
* setState → function to update value
* initialValue → initial state

---

## Example: Counter

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  function increment() {
    setCount(count + 1);
  }

  return (
    <div>
      <p>{count}</p>
      <button onClick={increment}>+</button>
    </div>
  );
}
```

---

## What Happens Internally?

1. Component renders.
2. useState stores value internally.
3. setCount triggers re-render.
4. Component function runs again.
5. React updates only changed DOM.

---

# 3.3 — State vs Props

## Props

Definition:
Data passed from parent to child.

Characteristics:

* Immutable
* External to component

---

## State

Definition:
Data managed internally within component.

Characteristics:

* Mutable (via setter)
* Triggers re-render

---

## Example Comparison

```jsx
function Parent() {
  const [age, setAge] = useState(20);
  return <Child age={age} />;
}

function Child({ age }) {
  return <p>{age}</p>;
}
```

Parent owns state.
Child receives props.

---

# 3.4 — React Rendering & State Updates

Important rule:

Updating state causes component re-execution.

---

## Important Concept: Re-render ≠ DOM Rebuild

Re-render:

* Function runs again
* JSX recalculated

React compares new virtual tree and updates only differences.

---

# 3.5 — Functional Updates (Critical Concept)

Problem:

```jsx
setCount(count + 1);
setCount(count + 1);
```

Result:
Only increments once (because state is stale).

Correct way:

```jsx
setCount(prev => prev + 1);
setCount(prev => prev + 1);
```

Now increments twice.

---

## Why?

React batches state updates.
Functional form ensures correct previous value.

---

# 3.6 — Batching & Asynchronous Nature

State updates are asynchronous.

Example:

```jsx
setCount(count + 1);
console.log(count);
```

Console prints old value.

Because:
React schedules update.

---

# 3.7 — Controlled vs Uncontrolled Components

## Controlled Component

Definition:
Form input whose value is controlled by React state.

Example:

```jsx
function Form() {
  const [name, setName] = useState("");

  return (
    <input
      value={name}
      onChange={(e) => setName(e.target.value)}
    />
  );
}
```

React controls input value.

---

## Uncontrolled Component

Definition:
Form input managed by DOM, accessed via ref.

```jsx
import { useRef } from "react";

function Form() {
  const inputRef = useRef();

  function handleSubmit() {
    console.log(inputRef.current.value);
  }

  return <input ref={inputRef} />;
}
```

---

## Comparison

Controlled:

* More predictable
* Easier validation
* More re-renders

Uncontrolled:

* Less code
* Better performance for large forms

---

# 3.8 — Forms in React (Detailed)

## Example: Login Form

```jsx
function LoginForm() {
  const [form, setForm] = useState({
    email: "",
    password: ""
  });

  function handleChange(e) {
    const { name, value } = e.target;
    setForm(prev => ({
      ...prev,
      [name]: value
    }));
  }

  function handleSubmit(e) {
    e.preventDefault();
    console.log(form);
  }

  return (
    <form onSubmit={handleSubmit}>
      <input
        name="email"
        value={form.email}
        onChange={handleChange}
      />
      <input
        name="password"
        type="password"
        value={form.password}
        onChange={handleChange}
      />
      <button type="submit">Login</button>
    </form>
  );
}
```

---

## Key Concepts

* e.preventDefault() prevents page reload
* name attribute helps dynamic updates
* Spread operator preserves previous values

---

# 3.9 — Lifting State Up

## Definition

Moving state from child to nearest common parent so multiple components can share it.

---

## Example

Before lifting:

Two sibling components cannot share state.

After lifting:

```jsx
function Parent() {
  const [value, setValue] = useState("");

  return (
    <>
      <Input value={value} setValue={setValue} />
      <Display value={value} />
    </>
  );
}
```

Now both share same state.

---

# 3.10 — Derived State (Important Concept)

Definition:
State calculated from other state or props.

Bad pattern:

```jsx
const [filtered, setFiltered] = useState([]);
```

If filtered is derived from items,
you should compute instead:

```jsx
const filtered = items.filter(item => item.active);
```

Avoid duplicate sources of truth.

---

# 3.11 — State Colocation Principle

Keep state:
As close as possible to where it is used.

Avoid globalizing state unnecessarily.

---

# 3.12 — Immutable Update Patterns

React state must be treated as immutable.

---

## Updating Arrays

Add:

```jsx
setItems(prev => [...prev, newItem]);
```

Remove:

```jsx
setItems(prev => prev.filter(item => item.id !== id));
```

Update:

```jsx
setItems(prev =>
  prev.map(item =>
    item.id === id
      ? { ...item, completed: true }
      : item
  )
);
```

---

## Updating Objects

```jsx
setUser(prev => ({
  ...prev,
  age: 25
}));
```

Never mutate:

```jsx
user.age = 25; // ❌
```

---

# 3.13 — Common State Mistakes

1. Mutating state directly
2. Using index as key
3. Duplicating derived state
4. Forgetting functional update
5. Overusing global state

---

# 📘 Integrated Example — Todo App Core

```jsx
function TodoApp() {
  const [todos, setTodos] = useState([]);

  function addTodo(text) {
    const newTodo = {
      id: Date.now(),
      text,
      completed: false
    };

    setTodos(prev => [...prev, newTodo]);
  }

  function toggleTodo(id) {
    setTodos(prev =>
      prev.map(todo =>
        todo.id === id
          ? { ...todo, completed: !todo.completed }
          : todo
      )
    );
  }

  return (
    <div>
      {todos.map(todo => (
        <div key={todo.id}>
          <span
            style={{
              textDecoration: todo.completed
                ? "line-through"
                : "none"
            }}
          >
            {todo.text}
          </span>
          <button onClick={() => toggleTodo(todo.id)}>
            Toggle
          </button>
        </div>
      ))}
    </div>
  );
}
```

---

# 🧠 After Module 3 You Must Understand

* State drives UI
* useState triggers re-render
* State updates are asynchronous
* Functional updates prevent bugs
* Forms require controlled patterns
* Lifting state enables shared logic
* Immutable updates are mandatory

---

# 💡 Optional Exercises (Advanced)

1. Build a counter with increment by 5 using functional updates.
2. Create a registration form with validation.
3. Build a todo list with add, delete, toggle.
4. Demonstrate lifting state up with temperature converter.
5. Create a dynamic search filter without storing derived state.

---


