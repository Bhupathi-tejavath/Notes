
---

# 📘 MODULE 5 — Component Architecture & Patterns (Complete Deep Notes)

This module covers:

1. What is Component Architecture?
2. Reusable Components (Design Principles)
3. Presentational vs Container Components
4. Component Composition (Deep)
5. The `children` Prop (Advanced Usage)
6. Prop Drilling (Problem & Solutions)
7. Render Props Pattern
8. Higher Order Components (HOC)
9. Compound Components Pattern
10. Controlled Component Pattern (Advanced Use)
11. Separation of Concerns
12. Folder Structure for Scalable Apps
13. Common Architectural Mistakes
14. Integrated Example
15. Optional Exercises

---

# 5.1 — What is Component Architecture?

## Definition

Component architecture refers to the structured organization and design strategy used to build, compose, and manage React components in a scalable, maintainable, and reusable way.

In small apps, structure doesn’t matter much.

In large apps:

* Poor architecture leads to

  * Prop chaos
  * Tight coupling
  * Re-render performance issues
  * Difficult debugging

Good architecture ensures:

* Reusability
* Predictability
* Separation of concerns
* Scalability

---

# 5.2 — Reusable Components

## Definition

A reusable component is a self-contained UI unit that can be used multiple times across an application with different data.

---

## Poor Example (Non-reusable)

```jsx
function Button() {
  return <button style={{ background: "blue" }}>Click Me</button>;
}
```

Hardcoded text.
Hardcoded styling.
Not reusable.

---

## Proper Reusable Component

```jsx
function Button({ label, onClick, variant }) {
  const styles = {
    primary: "blue",
    danger: "red"
  };

  return (
    <button
      style={{ background: styles[variant] }}
      onClick={onClick}
    >
      {label}
    </button>
  );
}
```

Usage:

```jsx
<Button label="Save" variant="primary" />
<Button label="Delete" variant="danger" />
```

---

## Design Principles for Reusable Components

1. Accept configuration via props
2. Avoid hardcoded data
3. Keep logic minimal
4. Make styling configurable
5. Avoid coupling with global state

---

# 5.3 — Presentational vs Container Components

This is a conceptual separation pattern.

---

## Presentational Component

Definition:
A component responsible only for UI rendering.

Characteristics:

* Receives data via props
* No business logic
* No API calls
* No global state interaction

Example:

```jsx
function UserCard({ name, email }) {
  return (
    <div>
      <h3>{name}</h3>
      <p>{email}</p>
    </div>
  );
}
```

---

## Container Component

Definition:
A component responsible for logic, data fetching, and state management.

Example:

```jsx
function UserContainer() {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetch("/api/user")
      .then(res => res.json())
      .then(data => setUser(data));
  }, []);

  if (!user) return <p>Loading...</p>;

  return <UserCard name={user.name} email={user.email} />;
}
```

---

## Why This Separation Helps

* Easier testing
* Clear responsibility
* Better reusability
* Cleaner debugging

---

# 5.4 — Component Composition (Core React Strength)

## Definition

Composition is a pattern where components are built by combining smaller components.

Instead of inheritance, React promotes composition.

---

## Example

```jsx
function Layout({ children }) {
  return (
    <div>
      <Navbar />
      <main>{children}</main>
    </div>
  );
}
```

Usage:

```jsx
<Layout>
  <Dashboard />
</Layout>
```

Layout doesn’t know what children is.
It simply renders whatever is passed.

---

# 5.5 — The `children` Prop (Deep Understanding)

## Definition

`children` is a special prop that represents the content nested inside a component.

Example:

```jsx
<Card>
  <p>Hello</p>
</Card>
```

Inside Card:

```jsx
function Card({ children }) {
  return <div className="card">{children}</div>;
}
```

---

## Advanced Example

You can pass multiple elements:

```jsx
<Card>
  <h2>Title</h2>
  <p>Description</p>
</Card>
```

---

# 5.6 — Prop Drilling

## Definition

Prop drilling occurs when props are passed through multiple intermediate components that do not use them.

Example:

```jsx
<App>
  <Level1>
    <Level2>
      <Level3 user={user} />
    </Level2>
  </Level1>
</App>
```

Level1 and Level2 don’t use user.
They only forward it.

---

## Problems

* Hard to maintain
* Tightly coupled hierarchy
* Difficult refactoring

---

## Solutions

1. Context API
2. Global state (Redux, Zustand)
3. Component composition

---

# 5.7 — Render Props Pattern

## Definition

A pattern where a component shares logic by passing a function as a prop that returns JSX.

---

## Example

```jsx
function MouseTracker({ render }) {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  function handleMove(e) {
    setPosition({ x: e.clientX, y: e.clientY });
  }

  return (
    <div onMouseMove={handleMove}>
      {render(position)}
    </div>
  );
}
```

Usage:

```jsx
<MouseTracker
  render={({ x, y }) => (
    <p>Mouse at ({x}, {y})</p>
  )}
/>
```

This allows logic reuse without duplication.

---

# 5.8 — Higher Order Components (HOC)

## Definition

A Higher Order Component is a function that takes a component and returns a new component with additional behavior.

Pattern:

```id="g5x80r"
HOC(Component) → EnhancedComponent
```

---

## Example

```jsx
function withLogger(Component) {
  return function WrappedComponent(props) {
    console.log("Rendering", Component.name);
    return <Component {...props} />;
  };
}
```

Usage:

```jsx
const LoggedButton = withLogger(Button);
```

---

## When to Use HOC

* Cross-cutting concerns
* Logging
* Authentication
* Authorization

---

# 5.9 — Compound Components Pattern

## Definition

A pattern where multiple components work together as a single unit and share implicit state.

Example:

```jsx
<Tabs>
  <Tabs.List>
    <Tabs.Tab />
  </Tabs.List>
  <Tabs.Panel />
</Tabs>
```

Internally:
Tabs manages shared state.
Children consume it via context.

---

# 5.10 — Controlled Component Pattern (Advanced)

A controlled component allows the parent to control its internal state.

Example:

```jsx
function Toggle({ value, onChange }) {
  return (
    <button onClick={() => onChange(!value)}>
      {value ? "ON" : "OFF"}
    </button>
  );
}
```

Parent controls value.

This improves predictability.

---

# 5.11 — Separation of Concerns

Avoid mixing:

* UI rendering
* Business logic
* API logic
* Validation logic

Better structure:

```id="2clyh2"
components/
hooks/
services/
pages/
```

---

# 5.12 — Scalable Folder Structure

Example:

```id="6o4t41"
src/
├── components/
├── pages/
├── hooks/
├── services/
├── context/
└── utils/
```

Each folder has clear responsibility.

---

# 5.13 — Common Architectural Mistakes

1. Massive components (1000+ lines)
2. Tight coupling
3. Deep prop drilling
4. Business logic inside UI components
5. Duplicate logic instead of custom hooks

---

# 📘 Integrated Example

```jsx
function DashboardContainer() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    fetch("/api/users")
      .then(res => res.json())
      .then(data => setUsers(data));
  }, []);

  return <UserList users={users} />;
}

function UserList({ users }) {
  return (
    <div>
      {users.map(user => (
        <UserCard key={user.id} {...user} />
      ))}
    </div>
  );
}

function UserCard({ name, role }) {
  return (
    <div>
      <h3>{name}</h3>
      <p>{role}</p>
    </div>
  );
}
```

Clear separation.
Reusable UI.
Clean architecture.

---

# 🧠 After Module 5 You Must Understand

* Components are architectural units
* Composition > inheritance
* Separation of logic and UI
* Render props & HOC patterns
* Controlled vs uncontrolled patterns
* How to avoid prop drilling
* How to structure scalable apps

---

# 💡 Optional Exercises

1. Build a reusable Modal using composition.
2. Create a Tabs compound component.
3. Implement an HOC for authentication.
4. Refactor a large component into container + presentational.
5. Build a reusable FormField component.

---


