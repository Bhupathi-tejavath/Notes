
---

# 📘 MODULE 2 — React Core Fundamentals (Full Academic Notes)

---

# 2.1 — What is React?

## Definition

React is an open-source JavaScript library used for building user interfaces (UI), especially for single-page applications (SPAs), where UI needs to update dynamically without reloading the page.

React allows developers to build UI using small, reusable pieces called components. It uses a declarative programming model and a virtual DOM to efficiently update the user interface.

---

## Why React Was Created

Before React:

* Developers manually manipulated the DOM.
* UI logic and DOM updates were tightly coupled.
* Applications became difficult to maintain.

React solved this by:

* Making UI state-driven
* Automating DOM updates
* Enforcing one-way data flow

---

## Core Principles of React

### 1️⃣ Declarative UI

Instead of telling the browser how to update the UI step-by-step, you describe what the UI should look like for a given state.

Imperative (Traditional JS):

```javascript
document.getElementById("count").innerText = count;
```

Declarative (React):

```jsx
<p>{count}</p>
```

React handles:

* DOM comparison
* DOM updates
* Performance optimizations

---

### 2️⃣ Component-Based Architecture

Definition:
A component is a reusable, independent piece of UI logic that returns JSX.

Large UI is broken into smaller logical units:

```
App
 ├── Navbar
 ├── Sidebar
 └── Dashboard
      ├── Card
      └── Chart
```

Benefits:

* Reusability
* Maintainability
* Testability
* Scalability

---

### 3️⃣ Unidirectional Data Flow

Data flows:

Parent → Child (via props)

This makes application state predictable and easier to debug.

---

# 2.2 — SPA vs MPA

## SPA (Single Page Application)

Definition:
An application that loads a single HTML file and dynamically updates content using JavaScript without refreshing the page.

React primarily builds SPAs.

Flow:

1. Browser loads index.html
2. React mounts inside root div
3. Navigation happens using JavaScript

Example:
Gmail, Facebook, Instagram

---

## MPA (Multi Page Application)

Definition:
A traditional web application where each route loads a completely new HTML page from the server.

Example:
Older PHP-based websites.

---

# 2.3 — Virtual DOM

## Definition

The Virtual DOM is a lightweight JavaScript representation of the real DOM.

Instead of directly updating the real DOM (which is slow), React:

1. Creates a virtual representation
2. Compares changes
3. Updates only necessary parts

---

## Why Real DOM is Slow

Real DOM operations involve:

* Layout recalculations
* Repaints
* Reflows

These are expensive operations.

---

## How Virtual DOM Works (Step-by-step)

Example:

```jsx
<h1>Hello</h1>
```

React internally creates:

```javascript
{
  type: "h1",
  props: {
    children: "Hello"
  }
}
```

When state changes:

1. React creates a new virtual tree.
2. Compares it with the previous one.
3. Finds differences.
4. Applies minimal changes to real DOM.

This process is called:

> Reconciliation

---

# 2.4 — Reconciliation

## Definition

Reconciliation is the algorithm React uses to compare two virtual DOM trees and determine what needs to change in the real DOM.

---

## Example

Initial render:

```jsx
<p>Hello</p>
```

After state change:

```jsx
<p>Hello Pradeep</p>
```

React detects:
Only text changed → updates only text node.

---

## Why Keys Matter in Reconciliation

Consider:

```jsx
{items.map(item => (
  <li key={item.id}>{item.name}</li>
))}
```

Keys help React:

* Identify which item changed
* Prevent unnecessary re-renders
* Maintain component identity

Bad practice:

```jsx
key={index}
```

If order changes:
React cannot track correctly.

---

# 2.5 — JSX (Deep Explanation)

## Definition

JSX (JavaScript XML) is a syntax extension that allows writing HTML-like syntax inside JavaScript.

It is not HTML.
It compiles to JavaScript.

---

## JSX Compilation

JSX:

```jsx
<h1>Hello</h1>
```

Compiles to:

```javascript
React.createElement("h1", null, "Hello");
```

---

## JSX Rules (Detailed)

### 1️⃣ Must Return Single Root Element

Incorrect:

```jsx
return (
  <h1>Hello</h1>
  <p>World</p>
);
```

Correct:

```jsx
return (
  <>
    <h1>Hello</h1>
    <p>World</p>
  </>
);
```

---

### 2️⃣ JavaScript Expressions Inside {}

Valid:

```jsx
{2 + 2}
{user.name}
{items.length}
```

Invalid:

```jsx
{if (x) {}}
```

Use ternary instead.

---

### 3️⃣ Attributes Use CamelCase

```jsx
className
onClick
tabIndex
```

---

### 4️⃣ Self Closing Tags

```jsx
<img src="image.png" />
```

---

### 5️⃣ JSX Automatically Escapes Values

This prevents XSS attacks:

```jsx
<p>{userInput}</p>
```

Dangerous override:

```jsx
<div dangerouslySetInnerHTML={{ __html: htmlContent }} />
```

---

# 2.6 — Components

## Definition

A React component is a JavaScript function that:

* Accepts input (props)
* Returns JSX
* Can maintain internal state
* Can trigger re-renders

---

## Functional Component Syntax

```jsx
function Welcome() {
  return <h1>Hello</h1>;
}
```

Arrow function version:

```jsx
const Welcome = () => <h1>Hello</h1>;
```

---

## How Components Render

When React renders:

1. Calls component function.
2. Executes all code inside it.
3. Returns JSX.
4. Stores output in Virtual DOM.

When state updates:
Function executes again.

---

## Component Composition

```jsx
function App() {
  return (
    <div>
      <Navbar />
      <Content />
    </div>
  );
}
```

Components can contain other components.

---

# 2.7 — Props

## Definition

Props (short for properties) are read-only inputs passed from a parent component to a child component.

They allow data to flow down the component tree.

---

## Passing Props

```jsx
<Profile name="Pradeep" age={22} />
```

Receiving:

```jsx
function Profile({ name, age }) {
  return <p>{name} is {age}</p>;
}
```

---

## Props Are Immutable

You must never modify props inside a component.

Incorrect:

```javascript
props.name = "Rahul";
```

Correct approach:
Change parent state.

---

## Passing Functions as Props

```jsx
function Parent() {
  function handleClick() {
    alert("Clicked");
  }

  return <Child onClick={handleClick} />;
}

function Child({ onClick }) {
  return <button onClick={onClick}>Click</button>;
}
```

This enables child-to-parent communication.

---

# 2.8 — Event Handling

## Definition

React provides a synthetic event system that wraps native browser events to ensure cross-browser compatibility.

---

## Syntax

```jsx
<button onClick={handleClick}>Click</button>
```

Handler:

```jsx
function handleClick(event) {
  console.log(event.target);
}
```

---

## Passing Parameters

Correct:

```jsx
<button onClick={() => deleteUser(id)}>Delete</button>
```

Wrong:

```jsx
<button onClick={deleteUser(id)}>Delete</button>
```

---

# 2.9 — Conditional Rendering

Definition:
Rendering UI elements based on certain conditions.

---

## Ternary Operator

```jsx
{isLoggedIn ? <Dashboard /> : <Login />}
```

---

## Logical AND

```jsx
{isAdmin && <AdminPanel />}
```

---

## Early Return Pattern

```jsx
if (!user) return <Login />;
return <Dashboard />;
```

---

# 2.10 — Lists & Keys

## Definition

Rendering multiple elements dynamically using arrays.

---

## Example

```jsx
const items = ["A", "B", "C"];

<ul>
  {items.map(item => (
    <li key={item}>{item}</li>
  ))}
</ul>
```

---

## Why Keys Are Critical

Keys:

* Help React identify items
* Preserve component state
* Improve performance

---

# 2.11 — Styling in React

## Inline Styles

```jsx
<div style={{ backgroundColor: "blue", padding: "10px" }}>
```

Style is an object.

---

## External CSS

```jsx
import "./App.css";
```

---

## CSS Modules

```jsx
import styles from "./Button.module.css";

<button className={styles.primary}>Click</button>
```

---

# 📘 Module 2 — Complete Example

```jsx
function UserCard({ user, onDelete }) {
  return (
    <div className="card">
      <h2>{user.name}</h2>

      {user.isAdmin && <p>Admin User</p>}

      <button onClick={() => onDelete(user.id)}>
        Delete
      </button>
    </div>
  );
}

export default function App() {
  const users = [
    { id: 1, name: "Pradeep", isAdmin: true },
    { id: 2, name: "Rahul", isAdmin: false }
  ];

  function handleDelete(id) {
    console.log("Delete user:", id);
  }

  return (
    <div>
      {users.map(user => (
        <UserCard
          key={user.id}
          user={user}
          onDelete={handleDelete}
        />
      ))}
    </div>
  );
}
```

---

# 🧠 Module 2 — What You Must Understand Deeply

After this module you should clearly understand:

* React is declarative
* Components are functions
* Rendering re-executes components
* Virtual DOM optimizes updates
* Props are immutable
* Keys affect reconciliation
* Event system is synthetic
* JSX compiles to JavaScript

---

# 💡 Optional Advanced Exercises

1. Build a reusable `Modal` component.
2. Create a dashboard layout with nested components.
3. Render 20 items and test performance with wrong vs correct keys.
4. Inspect re-renders using React DevTools.

---


