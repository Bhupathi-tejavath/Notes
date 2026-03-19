
---

# 📘 MODULE 6 — Routing (Client-Side Navigation)

---

# 📑 Module Structure

1. What is Routing?
2. Client-Side vs Server-Side Routing
3. What is React Router?
4. Installing & Setting Up React Router
5. Core Routing Components

   * BrowserRouter
   * Routes
   * Route
6. Route Matching & Path Patterns
7. Nested Routes
8. Dynamic Routes (URL Parameters)
9. Query Parameters
10. Navigation

* Link
* NavLink
* useNavigate

11. useParams
12. Protected Routes (Authentication Pattern)
13. Lazy Loading Routes
14. Layout Routes
15. 404 Not Found Routes
16. Route-Based Code Splitting
17. Common Routing Mistakes
18. Integrated Example
19. Optional Exercises

---

# 6.1 — What is Routing?

## Definition

Routing is the mechanism that determines which UI component should be displayed based on the current URL in the browser.

In traditional websites:

* URL → Server → HTML page

In React:

* URL → React Router → Component render
* No full page reload

---

# 6.2 — Client-Side vs Server-Side Routing

## Server-Side Routing

Definition:
The server sends a new HTML page for each route request.

Flow:

1. User visits `/about`
2. Browser sends request to server
3. Server responds with full HTML page

---

## Client-Side Routing

Definition:
The browser loads a single HTML file once. JavaScript controls route changes without refreshing the page.

Flow:

1. User clicks link
2. URL changes
3. React Router updates component
4. No reload

Benefits:

* Faster navigation
* Better user experience
* Reduced server load

---

# 6.3 — What is React Router?

## Definition

React Router is a standard library for routing in React applications. It enables dynamic routing, nested routes, protected routes, and navigation without reloading the page.

React Router DOM is used for web applications.

---

# 6.4 — Installation & Setup

Install:

```bash
npm install react-router-dom
```

---

## Basic Setup

In main.jsx:

```jsx
import { BrowserRouter } from "react-router-dom";

createRoot(document.getElementById("root")).render(
  <BrowserRouter>
    <App />
  </BrowserRouter>
);
```

BrowserRouter must wrap the entire app.

---

# 6.5 — Core Routing Components

---

## 6.5.1 BrowserRouter

### Definition

BrowserRouter uses HTML5 History API to keep UI in sync with URL.

It listens to URL changes and re-renders matching routes.

---

## 6.5.2 Routes

Container for all Route definitions.

```jsx
<Routes>
  <Route path="/" element={<Home />} />
</Routes>
```

---

## 6.5.3 Route

Defines a mapping between a URL path and a component.

```jsx
<Route path="/about" element={<About />} />
```

When URL matches `/about`, About component renders.

---

# 6.6 — Route Matching & Path Patterns

Routes match based on path.

Example:

```jsx
<Route path="/dashboard" element={<Dashboard />} />
```

If URL is `/dashboard`, component renders.

---

## Exact Matching

React Router v6 matches exactly by default.

---

# 6.7 — Nested Routes

## Definition

Nested routes allow rendering child components inside parent layouts.

---

## Example

```jsx
<Routes>
  <Route path="/dashboard" element={<Dashboard />}>
    <Route path="profile" element={<Profile />} />
  </Route>
</Routes>
```

Dashboard must include:

```jsx
import { Outlet } from "react-router-dom";

function Dashboard() {
  return (
    <>
      <h1>Dashboard</h1>
      <Outlet />
    </>
  );
}
```

Outlet renders nested route.

---

# 6.8 — Dynamic Routes (URL Parameters)

## Definition

Dynamic routes allow capturing values from URL.

Example:

```jsx
<Route path="/user/:id" element={<User />} />
```

If URL is `/user/10`, id = 10.

---

## Accessing Parameter

```jsx
import { useParams } from "react-router-dom";

function User() {
  const { id } = useParams();
  return <h2>User ID: {id}</h2>;
}
```

---

# 6.9 — Query Parameters

Example URL:

```id="example"
http://localhost:5173/search?q=react
```

Access query parameters:

```jsx
import { useLocation } from "react-router-dom";

function Search() {
  const { search } = useLocation();
  const queryParams = new URLSearchParams(search);
  const query = queryParams.get("q");

  return <p>Search query: {query}</p>;
}
```

---

# 6.10 — Navigation

---

## 6.10.1 Link Component

Use instead of `<a>` tag.

```jsx
import { Link } from "react-router-dom";

<Link to="/about">About</Link>
```

Why not `<a>`?
`<a>` reloads page.

---

## 6.10.2 NavLink

Like Link but provides active styling.

```jsx
<NavLink to="/home">
  {({ isActive }) => (
    <span style={{ color: isActive ? "red" : "black" }}>
      Home
    </span>
  )}
</NavLink>
```

---

## 6.10.3 useNavigate

Programmatic navigation.

```jsx
import { useNavigate } from "react-router-dom";

function Login() {
  const navigate = useNavigate();

  function handleLogin() {
    navigate("/dashboard");
  }
}
```

---

# 6.11 — Protected Routes

## Definition

A route that requires authentication before access.

---

## Example

```jsx
function ProtectedRoute({ children }) {
  const isAuthenticated = true; // example

  if (!isAuthenticated) {
    return <Navigate to="/login" />;
  }

  return children;
}
```

Usage:

```jsx
<Route
  path="/dashboard"
  element={
    <ProtectedRoute>
      <Dashboard />
    </ProtectedRoute>
  }
/>
```

---

# 6.12 — Layout Routes

Used for shared layouts.

Example:

```jsx
<Route path="/" element={<Layout />}>
  <Route index element={<Home />} />
  <Route path="about" element={<About />} />
</Route>
```

Layout must include:

```jsx
<Outlet />
```

---

# 6.13 — 404 Not Found Route

```jsx
<Route path="*" element={<NotFound />} />
```

Matches any undefined route.

---

# 6.14 — Lazy Loading Routes

Improves performance.

```jsx
import { lazy, Suspense } from "react";

const Dashboard = lazy(() => import("./Dashboard"));

<Suspense fallback={<p>Loading...</p>}>
  <Dashboard />
</Suspense>
```

Used for route-level code splitting.

---

# 6.15 — Common Routing Mistakes

1. Using `<a>` instead of `<Link>`
2. Forgetting `<Outlet />` in nested routes
3. Incorrect dynamic parameter names
4. No 404 route
5. Not protecting sensitive routes

---

# 📘 Integrated Routing Example

```jsx
import {
  BrowserRouter,
  Routes,
  Route,
  Link
} from "react-router-dom";

function Home() {
  return <h1>Home</h1>;
}

function About() {
  return <h1>About</h1>;
}

function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="*" element={<h1>404</h1>} />
      </Routes>
    </BrowserRouter>
  );
}
```

---

# 🧠 After Module 6 You Must Understand

* How client-side routing works
* Route matching logic
* Dynamic URL parameters
* Query parameters
* Programmatic navigation
* Nested routes with Outlet
* Protected route pattern
* Lazy loading routes
* 404 handling

---

# 💡 Optional Exercises

1. Create a blog app with dynamic route `/post/:id`
2. Implement login + protected dashboard
3. Create nested dashboard routes
4. Add active navigation styling
5. Implement lazy loading for at least 2 pages

---


