
# 📘 MODULE 4 — React Hooks (Core & Advanced)

This module requires deep conceptual clarity.
Hooks fundamentally changed how React components manage state and side effects.

We will cover:

1. What Are Hooks? (Formal Definition)
2. Why Hooks Were Introduced
3. Rules of Hooks
4. useEffect (Extremely Detailed)
5. useRef (Deep Understanding)
6. useMemo
7. useCallback
8. useReducer
9. Custom Hooks
10. useLayoutEffect
11. useId
12. startTransition (React 18)
13. Automatic Batching
14. Common Hook Mistakes
15. Integrated Examples
16. Optional Exercises

No surface explanations. Full clarity.

---

# 4.1 — What Are Hooks?

## Definition

Hooks are special built-in functions in React that allow functional components to use state, lifecycle features, and other React capabilities without writing class components.

Before Hooks:

* Only class components could manage state and lifecycle.
* Functional components were stateless.

Hooks were introduced in React 16.8 to unify stateful logic in functional components.

---

# 4.2 — Why Hooks Were Introduced

### Problems With Class Components

1. Complex lifecycle methods
2. this binding confusion
3. Logic scattered across lifecycle methods
4. Hard to reuse stateful logic

Hooks solve:

* Reusability via custom hooks
* Simpler syntax
* Cleaner component logic

---

# 4.3 — Rules of Hooks (Critical)

React enforces strict rules.

## Rule 1:

Only call Hooks at the top level.

Correct:

```jsx
const [count, setCount] = useState(0);
```

Incorrect:

```jsx
if (condition) {
  useState(0); // ❌
}
```

Hooks must be called in same order on every render.

---

## Rule 2:

Only call Hooks inside React functions.

* Functional components
* Custom hooks

Not inside:

* Regular JavaScript functions
* Loops
* Conditions

---

# 4.4 — useEffect (Deep Dive)

## Definition

useEffect is a Hook that allows you to perform side effects in functional components.

Side effects include:

* API calls
* Subscriptions
* Timers
* DOM manipulation
* Logging

---

## Basic Syntax

```jsx
useEffect(() => {
  // effect logic
}, [dependencies]);
```

---

## How useEffect Works

After render:
React executes effect.

If dependencies change:
Effect runs again.

---

## Case 1: Run Once (On Mount)

```jsx
useEffect(() => {
  console.log("Component mounted");
}, []);
```

Empty dependency array → runs only once.

---

## Case 2: Run On Dependency Change

```jsx
useEffect(() => {
  console.log("Count changed");
}, [count]);
```

---

## Case 3: Run On Every Render

```jsx
useEffect(() => {
  console.log("Runs every render");
});
```

No dependency array.

---

## Cleanup Function

Important for:

* Removing event listeners
* Clearing timers
* Avoiding memory leaks

Example:

```jsx
useEffect(() => {
  const interval = setInterval(() => {
    console.log("Running");
  }, 1000);

  return () => {
    clearInterval(interval);
  };
}, []);
```

Cleanup runs:

* Before next effect
* On unmount

---

# 4.5 — useRef

## Definition

useRef creates a mutable object that persists across renders without causing re-render when updated.

---

## Syntax

```jsx
const ref = useRef(initialValue);
```

Access value:

```jsx
ref.current
```

---

## Use Case 1: Access DOM

```jsx
function InputFocus() {
  const inputRef = useRef();

  function focusInput() {
    inputRef.current.focus();
  }

  return (
    <>
      <input ref={inputRef} />
      <button onClick={focusInput}>Focus</button>
    </>
  );
}
```

---

## Use Case 2: Persist Value Without Re-render

```jsx
const renderCount = useRef(0);
renderCount.current++;
```

---

# 4.6 — useMemo

## Definition

useMemo memoizes (caches) the result of an expensive calculation to avoid recomputation on every render.

---

## Syntax

```jsx
const memoizedValue = useMemo(() => {
  return computeExpensiveValue(data);
}, [data]);
```

---

## Example

```jsx
const squared = useMemo(() => {
  console.log("Calculating...");
  return num * num;
}, [num]);
```

Without useMemo:
Calculation runs every render.

---

# 4.7 — useCallback

## Definition

useCallback memoizes a function so that it is not recreated on every render unless dependencies change.

---

## Syntax

```jsx
const memoizedFn = useCallback(() => {
  doSomething();
}, [dependencies]);
```

---

## Why It Matters

Useful when:

* Passing functions to child components
* Preventing unnecessary re-renders

---

# 4.8 — useReducer

## Definition

useReducer is an alternative to useState for complex state logic.

It follows reducer pattern:

```
(state, action) → newState
```

---

## Syntax

```jsx
const [state, dispatch] = useReducer(reducer, initialState);
```

---

## Example

```jsx
function reducer(state, action) {
  switch (action.type) {
    case "increment":
      return { count: state.count + 1 };
    default:
      return state;
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, { count: 0 });

  return (
    <>
      <p>{state.count}</p>
      <button onClick={() => dispatch({ type: "increment" })}>
        +
      </button>
    </>
  );
}
```

---

# 4.9 — Custom Hooks

## Definition

A custom hook is a JavaScript function that uses other hooks and starts with "use".

Example:

```jsx
function useCounter(initialValue) {
  const [count, setCount] = useState(initialValue);

  function increment() {
    setCount(prev => prev + 1);
  }

  return { count, increment };
}
```

Usage:

```jsx
const { count, increment } = useCounter(0);
```

Custom hooks allow reusable state logic.

---

# 4.10 — useLayoutEffect

## Definition

Similar to useEffect but runs synchronously after DOM updates and before browser paints.

Use when:

* Measuring DOM
* Layout calculations

---

# 4.11 — useId

Generates unique stable IDs.

```jsx
const id = useId();
<label htmlFor={id}>Name</label>
<input id={id} />
```

Useful for accessibility.

---

# 4.12 — startTransition (React 18)

## Definition

Allows marking state updates as non-urgent.

```jsx
import { startTransition } from "react";

startTransition(() => {
  setSearchQuery(query);
});
```

Improves UI responsiveness.

---

# 4.13 — Automatic Batching

React 18 batches multiple state updates into one render automatically.

Example:

```jsx
setCount(c => c + 1);
setFlag(f => !f);
```

Only one re-render.

---

# 4.14 — Common Hook Mistakes

1. Missing dependencies in useEffect
2. Infinite loops
3. Overusing useMemo
4. Not cleaning up effects
5. Calling hooks conditionally

---

# 📘 Integrated Example

```jsx
function SearchApp() {
  const [query, setQuery] = useState("");
  const [results, setResults] = useState([]);

  useEffect(() => {
    if (!query) return;

    const timeout = setTimeout(() => {
      fetch(`https://api.example.com/search?q=${query}`)
        .then(res => res.json())
        .then(data => setResults(data));
    }, 500);

    return () => clearTimeout(timeout);
  }, [query]);

  return (
    <>
      <input
        value={query}
        onChange={e => setQuery(e.target.value)}
      />
      <ul>
        {results.map(item => (
          <li key={item.id}>{item.name}</li>
        ))}
      </ul>
    </>
  );
}
```

---

# 🧠 After Module 4 You Must Understand

* Hooks replace class lifecycle
* useEffect controls side effects
* useRef stores persistent mutable values
* useMemo optimizes expensive calculations
* useCallback stabilizes functions
* useReducer handles complex state
* Custom hooks enable reuse
* startTransition improves UX

---

# 💡 Optional Exercises

1. Build a stopwatch using useEffect.
2. Create a custom hook for form handling.
3. Implement a reducer-based counter.
4. Build a debounced search input.
5. Use useMemo to optimize heavy calculations.

---


