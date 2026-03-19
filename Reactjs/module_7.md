

---

# 📘 MODULE 7 — Global State Management (Deep & Architectural)

---

# 📑 Module Structure

1. What is Global State?
2. Local State vs Global State
3. When Do You Need Global State?
4. Problems Without Proper Global State
5. React Context API (Deep)

   * Definition
   * createContext
   * Provider
   * useContext
6. Context + useReducer Pattern
7. Reducer Pattern (Architectural)
8. Designing Global State Shape
9. Avoiding Context Re-render Problems
10. Middleware Concept (Intro)
11. Redux (Conceptual Deep Understanding)
12. Redux Toolkit (Modern Approach)
13. Zustand (Lightweight Alternative)
14. State Normalization
15. Global vs Server State (Important Distinction)
16. Common Global State Mistakes
17. Integrated Architecture Example
18. Optional Exercises

---

# 7.1 — What is Global State?

## Definition

Global state refers to application-level data that needs to be accessed or modified by multiple components across different parts of the component tree.

Unlike local state (useState), global state is:

* Shared across components
* Centralized
* Often persisted
* Often critical (auth, theme, user, settings)

---

# 7.2 — Local State vs Global State

## Local State

Definition:
State confined to a specific component.

Example:

```jsx
const [count, setCount] = useState(0);
```

Only used in that component.

---

## Global State

Definition:
State accessible from multiple components without prop drilling.

Example:

* Logged-in user
* Dark/light theme
* Cart items
* Auth token

---

# 7.3 — When Do You Need Global State?

You need global state when:

1. Multiple distant components need same data.
2. Prop drilling becomes excessive.
3. Authentication status affects entire app.
4. Theme preference must apply globally.
5. Data must persist across routes.

---

# 7.4 — Problem Without Proper Global State

Example of prop drilling:

```jsx
<App>
  <Level1>
    <Level2>
      <Level3 user={user} />
    </Level2>
  </Level1>
</App>
```

Level1 & Level2 don’t use user.
They just forward it.

Problems:

* Maintenance difficulty
* Tight coupling
* Hard refactoring

---

# 7.5 — React Context API (Deep Understanding)

## Definition

Context API provides a way to share values between components without passing props manually at every level.

It creates a global-like state within a specific subtree.

---

# 7.5.1 — createContext

```jsx
import { createContext } from "react";

const AuthContext = createContext();
```

Creates a context object.

---

# 7.5.2 — Provider

Provider makes context value available to children.

```jsx
<AuthContext.Provider value={{ user }}>
  <App />
</AuthContext.Provider>
```

All components inside can access user.

---

# 7.5.3 — useContext

Access context inside component:

```jsx
import { useContext } from "react";

function Profile() {
  const { user } = useContext(AuthContext);
  return <p>{user.name}</p>;
}
```

---

# 7.6 — Context + useReducer Pattern (Recommended Pattern)

For scalable state, combine:

* Context → Global accessibility
* useReducer → Predictable state updates

---

## Example

### Step 1 — Reducer

```jsx
function authReducer(state, action) {
  switch (action.type) {
    case "LOGIN":
      return { ...state, user: action.payload };
    case "LOGOUT":
      return { ...state, user: null };
    default:
      return state;
  }
}
```

---

### Step 2 — Provider

```jsx
const AuthContext = createContext();

function AuthProvider({ children }) {
  const [state, dispatch] = useReducer(authReducer, {
    user: null
  });

  return (
    <AuthContext.Provider value={{ state, dispatch }}>
      {children}
    </AuthContext.Provider>
  );
}
```

---

### Step 3 — Usage

```jsx
function Login() {
  const { dispatch } = useContext(AuthContext);

  function handleLogin(userData) {
    dispatch({ type: "LOGIN", payload: userData });
  }
}
```

---

# 7.7 — Reducer Pattern (Architectural Understanding)

## Definition

A reducer is a pure function that takes:

```plaintext
(currentState, action) → newState
```

---

## Why Use Reducers?

* Centralized logic
* Predictable state transitions
* Easier debugging
* Easier testing

---

# 7.8 — Designing Global State Shape

Bad design:

```javascript
{
  userName,
  userEmail,
  userRole,
  isLoggedIn,
  token
}
```

Better design:

```javascript
{
  auth: {
    user: {},
    token: "",
    isAuthenticated: false
  },
  theme: "dark",
  cart: []
}
```

Group logically related state.

---

# 7.9 — Avoiding Context Re-render Problem

Important:

Every time context value changes,
ALL consumers re-render.

Solution:

* Split contexts
* Memoize values

Example:

```jsx
const value = useMemo(() => ({ state, dispatch }), [state]);
```

---

# 7.10 — Middleware Concept (Intro)

Middleware intercepts actions before reducer handles them.

Used for:

* Logging
* Async API calls
* Debugging

Redux supports middleware deeply.

---

# 7.11 — Redux (Conceptual Deep Explanation)

## Definition

Redux is a predictable state container for JavaScript apps.

It enforces:

1. Single source of truth
2. State is read-only
3. Changes via pure functions (reducers)

---

## Core Concepts

* Store
* Action
* Reducer
* Dispatch

---

## Flow

```plaintext
UI → dispatch(action) → reducer → new state → UI updates
```

---

# 7.12 — Redux Toolkit (Modern Redux)

Redux Toolkit simplifies Redux setup.

Example:

```jsx
import { createSlice } from "@reduxjs/toolkit";

const counterSlice = createSlice({
  name: "counter",
  initialState: { value: 0 },
  reducers: {
    increment: state => {
      state.value++;
    }
  }
});
```

Uses Immer internally to allow "mutating" syntax safely.

---

# 7.13 — Zustand (Lightweight Alternative)

## Definition

Zustand is a minimal global state library without boilerplate.

Example:

```jsx
import create from "zustand";

const useStore = create(set => ({
  count: 0,
  increment: () => set(state => ({ count: state.count + 1 }))
}));
```

Usage:

```jsx
const { count, increment } = useStore();
```

No provider needed.

---

# 7.14 — State Normalization

Definition:
Storing data in flat structure to avoid duplication.

Bad:

```javascript
{
  posts: [
    { id: 1, user: { id: 10, name: "A" } }
  ]
}
```

Better:

```javascript
{
  posts: { 1: { id: 1, userId: 10 } },
  users: { 10: { id: 10, name: "A" } }
}
```

Improves performance and avoids duplication.

---

# 7.15 — Global State vs Server State

Important distinction:

Global state:

* UI state
* Auth state
* Theme

Server state:

* Data from API
* Cached responses

Modern apps use:

* React Query / TanStack Query for server state
* Context/Redux for UI state

---

# 7.16 — Common Global State Mistakes

1. Overusing global state
2. Storing derived state
3. Not splitting contexts
4. Putting everything in Redux
5. Mutating state directly

---

# 📘 Integrated Architecture Example

```jsx
<AuthProvider>
  <ThemeProvider>
    <App />
  </ThemeProvider>
</AuthProvider>
```

Inside App:

```jsx
const { state } = useContext(AuthContext);

if (!state.user) {
  return <Login />;
}

return <Dashboard />;
```

Clear separation.
Predictable logic.
Scalable design.

---

# 🧠 After Module 7 You Must Understand

* When to use global state
* Context API deeply
* Reducer architecture
* Avoid prop drilling properly
* Redux principles
* State normalization
* Server vs UI state separation
* How to design scalable global state

---

# 💡 Optional Exercises

1. Build an AuthContext with login/logout.
2. Implement theme switcher using Context.
3. Refactor local cart state into global store.
4. Build reducer for todo app.
5. Normalize nested user-post data.

---


