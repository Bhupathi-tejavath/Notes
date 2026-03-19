

---

# 📘 MODULE 1 — Prerequisites (Foundation Layer)

This module ensures you understand the JavaScript and tooling fundamentals required before diving into React. React **isn’t a standalone ecosystem** — it builds on modern JavaScript. A weak foundation here slows you down drastically.

---

## 🧠 Module 1 Overview

**Topics Covered:**

1. Modern JavaScript (ES6+) essentials

   * let / const
   * Arrow functions
   * Destructuring
   * Spread & Rest
   * Template literals
   * Modules (import/export)
   * Promises & async/await
   * Array methods (map, filter, reduce)
   * Optional chaining & nullish coalescing
   * Closures (conceptual)

2. Node.js & npm

   * What is Node?
   * npm basics
   * package.json
   * scripts

3. Vite (recommended build tool / dev server)

---

# 📍 1.1 — Modern JavaScript (ES6+) — In-Depth

Modern React code relies completely on ES6+. Here’s each topic explained with examples.

---

## 📌 1.1.1 — let and const

### Definition

* `let`: Block-scoped variable that *can* be reassigned.
* `const`: Block-scoped constant that *cannot* be reassigned.

### Why use them?

They replace `var`, which is function-scoped and leads to hard-to-debug bugs.

### Syntax

```javascript
let age = 20;
age = 21; // valid

const name = "Pradeep";
name = "John"; // ❌ error
```

### Important

`const` protects the *binding* — not the *contents* of an object.

```javascript
const config = { theme: "dark" };
config.theme = "light"; // valid
```

---

## 📌 1.1.2 — Arrow Functions

### Definition

Shorter syntax for functions; do *not* have their own `this`.

### Syntax

```javascript
const add = (x, y) => x + y;
```

Equivalent to:

```javascript
function add(x, y) {
  return x + y;
}
```

---

## 📌 1.1.3 — Destructuring

Extract values cleanly from objects or arrays.

### Object destructuring

```javascript
const user = { name: "Priya", score: 90 };
const { name, score } = user;
```

### Array destructuring

```javascript
const [first, second] = [10, 20];
```

---

## 📌 1.1.4 — Spread and Rest Operators

Both use `...` but behave differently.

### Spread — expand an array or object

```javascript
const arr = [1,2,3];
const newArr = [...arr, 4];
```

### Rest — collect remaining

```javascript
function sum(...numbers) {
  return numbers.reduce((a, b) => a + b, 0);
}
```

---

## 📌 1.1.5 — Template Literals

Interpolation using backticks `` ` ``

```javascript
let name = "Pradeep";
console.log(`Hello ${name}!`);
```

---

## 📌 1.1.6 — Modules

React and modern tools **depend on modules**.

### Named Export

```javascript
// math.js
export function add(a,b) { return a + b; }
```

### Default Export

```javascript
// utils.js
export default function log(x) { console.log(x); }
```

### Import

```javascript
import { add } from "./math.js";
import log from "./utils.js";
```

---

## 📌 1.1.7 — Promises and async/await

### Promise Example

```javascript
fetch("https://api.example.com/users")
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error(error));
```

### Async/Await

```javascript
async function loadData() {
  try {
    const res = await fetch("https://api.example.com/users");
    const data = await res.json();
    console.log(data);
  } catch (err) {
    console.error(err);
  }
}
loadData();
```

---

## 📌 1.1.8 — Array methods (map, filter, reduce)

### map

Transforms each item:

```javascript
const nums = [1,2,3];
const doubled = nums.map(x => x * 2);
```

### filter

Returns subset

```javascript
const evens = nums.filter(x => x % 2 === 0);
```

### reduce

Accumulates value

```javascript
const sum = nums.reduce((a, b) => a + b, 0);
```

---

## 📌 1.1.9 — Optional Chaining & Nullish Coalescing

### Optional Chaining `?.`

Safely access deep value without errors:

```javascript
const user = {};
console.log(user.profile?.name); // undefined
```

### Nullish Coalescing `??`

Only uses fallback if value is `null` or `undefined`:

```javascript
let x = null;
console.log(x ?? "default"); // "default"
```

---

## 📌 1.1.10 — Closures (Conceptual)

A closure is a function that *remembers* the scope in which it was created.

```javascript
function createCounter() {
  let count = 0;
  return function () {
    count++;
    return count;
  };
}

const counter = createCounter();
console.log(counter()); // 1
```

Closures are critical in hooks and custom function factories.

---

# 📍 1.2 — Node.js & npm

React apps are built and served using Node tooling.

---

## 📌 1.2.1 — What is Node.js?

Node.js is a JavaScript runtime that executes JS outside the browser.

This enables:
✔ Local dev servers
✔ Build tools
✔ npm package installations
✔ Scripts (`start`, `dev`, `build`)

---

## 📌 1.2.2 — Installing Node & npm

Check installation:

```bash
node -v
npm -v
```

You get `node` and `npm` together.

---

## 📌 1.2.3 — package.json

`package.json` stores:
✔ Project name & version
✔ Dependencies
✔ Scripts
✔ Metadata

Generate it:

```bash
npm init -y
```

### Scripts section

Example:

```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "start": "vite preview"
  }
}
```

* `npm run dev`: start dev server
* `npm run build`: production build
* `npm run start`: preview prod build

---

# 📍 1.3 — Vite (Dev Server & Bundler)

React needs a development server. Vite is currently the industry standard.

---

## 📌 1.3.1 — What is Vite?

* Fast dev server
* Uses native ES modules
* Instant updates on file save
* Lightweight build config

---

## 📌 1.3.2 — Initialize a React Project with Vite

### Step-by-Step

1. Create project

```bash
npm create vite@latest my-react-app -- --template react
```

2. Install dependencies

```bash
cd my-react-app
npm install
```

3. Start dev server

```bash
npm run dev
```

You’ll get a local URL like `http://localhost:5173`

---

## 📌 1.3.3 — Main Files in Vite

```
├── index.html      // entry point
├── package.json
├── vite.config.js  // config file
└── src/
    ├── main.jsx    // React entry
    └── App.jsx     // root component
```

### Explanation

* `index.html` has a `<div id="root"></div>` — the mount point.
* `main.jsx` renders your React app into that `div`.

Example `main.jsx`:

```jsx
import React from "react";
import { createRoot } from "react-dom/client";
import App from "./App.jsx";

createRoot(document.getElementById("root")).render(<App />);
```

---

# 📍 Module 1 — Quick Examples

### Example: Using ES6 features together

```javascript
// spread + destructure
const user = { name: "Pradeep", age: 22 };
const { name, ...rest } = user;
const updatedUser = { ...rest, role: "student" };

console.log(updatedUser);
```

---

# 💡 Module 1 — Optional Exercises

1. Initialize a new `npm` project and add the scripts `dev`, `build`, and `start`. Explain what each one does.
2. Write a short JS program that fetches a public API using `async/await`.
3. Create an array of numbers and print the doubled values using `.map()`.
4. Build a small Vite-React project and locate where React is mounted in the DOM.

---

