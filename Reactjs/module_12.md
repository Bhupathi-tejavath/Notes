
---

# 📘 MODULE 12 — Advanced React

(Fiber, Concurrency, Error Boundaries, Portals, Refs & Internal Architecture)

---

# 📑 Module Structure

1. Why Advanced React Concepts Matter
2. React Fiber Architecture
3. Render Phase vs Commit Phase
4. Reconciliation (Deep Internal Model)
5. Concurrent Rendering (React 18)
6. StrictMode (Development Behavior)
7. Error Boundaries (Deep)
8. Portals
9. Forward Ref
10. useImperativeHandle
11. Advanced Suspense
12. startTransition (Concurrency Revisited)
13. Batching (Legacy vs Automatic)
14. Hydration (Conceptual Intro)
15. Server Components (Conceptual Intro)
16. Advanced Composition Patterns
17. Common Advanced Pitfalls
18. Integrated Example
19. Advanced Exercises

---

# 12.1 — Why Advanced React Concepts Matter

When applications scale:

* Rendering becomes complex
* UI updates must remain responsive
* Errors must not crash entire app
* Modals must escape DOM hierarchy
* Performance must remain smooth

Understanding internals allows you to:

* Debug re-render issues
* Build high-performance interfaces
* Avoid subtle concurrency bugs
* Design stable architectures

---

# 12.2 — React Fiber Architecture

## Formal Definition

React Fiber is the reimplementation of React’s core reconciliation algorithm introduced in React 16, designed to enable incremental rendering, prioritization of updates, and interruption of work for better responsiveness.

---

## Before Fiber

React used stack-based reconciliation:

* Render had to complete fully
* Could not pause work
* Long renders blocked UI

---

## After Fiber

Fiber enables:

* Breaking work into units
* Pausing rendering
* Prioritizing updates
* Resuming later

---

## What is a Fiber?

A Fiber is a JavaScript object representing a unit of work for a component.

Each component corresponds to a fiber node.

Fiber stores:

* Component type
* Props
* State
* Child references
* Effect flags

---

## Why Fiber Exists

To enable:

* Concurrent rendering
* Suspense
* startTransition
* Better scheduling

---

# 12.3 — Render Phase vs Commit Phase

React updates occur in two phases.

---

## 1️⃣ Render Phase

* React calls component functions
* Builds new Virtual DOM
* Calculates differences
* Can be paused/interrupted (in concurrent mode)

No DOM changes yet.

---

## 2️⃣ Commit Phase

* Applies changes to real DOM
* Runs layout effects
* Cannot be interrupted

---

## Important

Side effects (useEffect) run after commit phase.

---

# 12.4 — Reconciliation (Deep Internal Model)

Reconciliation compares:

Old Fiber Tree
vs
New Fiber Tree

React performs:

1. Element type comparison
2. Key comparison
3. Child diffing

---

## Key Matching Algorithm

When keys match:

* React reuses component

When keys change:

* React destroys old and creates new

This affects:

* Component state
* Performance
* DOM stability

---

# 12.5 — Concurrent Rendering (React 18)

## Definition

Concurrent rendering allows React to prepare multiple versions of UI in memory without blocking the main thread.

---

## Why It Matters

Without concurrency:

* Large updates block UI
* Typing can feel laggy

With concurrency:

* Urgent updates prioritized
* Heavy rendering deferred

---

## Example Scenario

User typing in search input:

* Typing must feel instant
* Search results rendering can be delayed

---

# 12.6 — StrictMode

## Definition

StrictMode is a development-only tool that highlights potential problems in React applications.

---

## Behavior

In development:

* Components render twice
* Effects run twice

This helps detect:

* Side-effect bugs
* Improper cleanup

---

# 12.7 — Error Boundaries

## Definition

Error boundaries are React components that catch JavaScript errors in their child component tree and display fallback UI instead of crashing the whole application.

---

## Why Needed?

If a component throws error:

Without boundary:
App crashes.

With boundary:
Fallback UI shown.

---

## Class-Based Implementation

```jsx
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError() {
    return { hasError: true };
  }

  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong</h1>;
    }

    return this.props.children;
  }
}
```

Wrap component:

```jsx
<ErrorBoundary>
  <Dashboard />
</ErrorBoundary>
```

---

## Important

Error boundaries catch:

* Rendering errors
* Lifecycle errors

They do NOT catch:

* Event handler errors
* Async errors

---

# 12.8 — Portals

## Definition

Portals allow rendering a component outside its parent DOM hierarchy while keeping it part of React tree.

---

## Why Needed?

For:

* Modals
* Tooltips
* Dropdowns

Avoid CSS overflow or z-index issues.

---

## Example

HTML:

```html
<div id="root"></div>
<div id="modal-root"></div>
```

React:

```jsx
import { createPortal } from "react-dom";

function Modal({ children }) {
  return createPortal(
    <div className="modal">{children}</div>,
    document.getElementById("modal-root")
  );
}
```

---

# 12.9 — Forward Ref

## Problem

By default, refs do not pass through components.

---

## Definition

forwardRef allows passing ref from parent to child DOM node.

---

## Example

```jsx
import { forwardRef } from "react";

const Input = forwardRef((props, ref) => {
  return <input ref={ref} {...props} />;
});
```

Usage:

```jsx
const inputRef = useRef();
<Input ref={inputRef} />
```

---

# 12.10 — useImperativeHandle

## Definition

Allows customizing what parent accesses when using ref.

---

## Example

```jsx
const CustomInput = forwardRef((props, ref) => {
  const inputRef = useRef();

  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current.focus();
    }
  }));

  return <input ref={inputRef} />;
});
```

Parent can call:

```jsx
inputRef.current.focus();
```

---

# 12.11 — Advanced Suspense

Suspense allows:

* Delaying UI rendering
* Showing fallback during lazy loading
* Streaming UI (future direction)

---

## Suspense with Lazy

```jsx
<Suspense fallback={<Spinner />}>
  <LazyComponent />
</Suspense>
```

---

# 12.12 — startTransition (Revisited)

Used for marking non-urgent state updates.

```jsx
import { startTransition } from "react";

startTransition(() => {
  setResults(data);
});
```

Typing remains smooth.

---

# 12.13 — Automatic Batching

Before React 18:
Only React event updates batched.

After React 18:
All updates batched automatically.

Example:

```jsx
setCount(c => c + 1);
setFlag(f => !f);
```

Single re-render.

---

# 12.14 — Hydration (Conceptual)

Hydration attaches event listeners to server-rendered HTML.

Important in SSR frameworks.

---

# 12.15 — Server Components (Conceptual)

Server components:

* Render on server
* Do not send JS to client
* Improve performance
* Reduce bundle size

Mostly used in Next.js.

---

# 12.16 — Advanced Composition Patterns

Includes:

* Compound components
* Headless UI pattern
* Controlled/uncontrolled hybrid

Used in component libraries.

---

# 12.17 — Common Advanced Pitfalls

1. Ignoring StrictMode warnings
2. Using keys incorrectly
3. Not cleaning effects
4. Using startTransition incorrectly
5. Mutating refs dangerously
6. Catching errors incorrectly

---

# 📘 Integrated Example (Modal with Portal + ForwardRef)

```jsx
const Modal = forwardRef(({ children }, ref) => {
  useImperativeHandle(ref, () => ({
    open: () => console.log("Opened")
  }));

  return createPortal(
    <div className="modal">{children}</div>,
    document.getElementById("modal-root")
  );
});
```

---

# 🧠 After Module 12 You Must Understand

* How Fiber schedules work
* Difference between render & commit
* Concurrent rendering principles
* How to handle errors safely
* How to use portals
* How to forward refs properly
* How batching works
* How Suspense works internally

---

