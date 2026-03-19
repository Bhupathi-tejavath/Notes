

---

# 📘 MODULE 8 — API Integration & Backend Communication (Production-Level Deep Dive)

---

# 📑 Module Structure

1. What is API Integration?
2. What is REST API?
3. HTTP Methods (Deep Understanding)
4. Fetch API (Detailed)
5. Axios (Detailed)
6. useEffect + API Calls (Correct Patterns)
7. Loading, Error, Success States (State Machine Thinking)
8. Async/Await vs Promises
9. Handling Race Conditions
10. Aborting Requests (Cleanup)
11. Authentication Flow (JWT-based)
12. Token Storage Strategies
13. Role-Based Access Integration
14. Interceptors (Axios Advanced)
15. Error Handling Strategy (Global + Local)
16. Custom API Hooks (Reusable Pattern)
17. File Upload Handling
18. CORS Deep Understanding
19. API Security Awareness (Frontend Perspective)
20. Optimistic UI Updates
21. Pagination, Filtering, Searching
22. Server State vs UI State
23. Common API Mistakes
24. Integrated Production Example
25. Optional Exercises

---

# 8.1 — What is API Integration?

## Definition

API integration in React refers to the process of communicating with a backend server using HTTP requests to fetch, create, update, or delete data.

React does NOT store persistent data.
Backend APIs do.

React:

* Sends requests
* Receives responses
* Updates UI

---

# 8.2 — What is REST API?

## Definition

REST (Representational State Transfer) is an architectural style for designing networked applications using HTTP methods.

Typical REST endpoints:

```
GET     /users
GET     /users/1
POST    /users
PUT     /users/1
DELETE  /users/1
```

---

# 8.3 — HTTP Methods (Deep Understanding)

### GET

Retrieve data.
Should not modify server state.

### POST

Create new resource.

### PUT

Replace existing resource.

### PATCH

Partially update resource.

### DELETE

Remove resource.

---

# 8.4 — Fetch API (Detailed)

Fetch is built into browsers.

---

## Basic GET Request

```jsx
useEffect(() => {
  fetch("https://jsonplaceholder.typicode.com/posts")
    .then(res => res.json())
    .then(data => console.log(data))
    .catch(err => console.error(err));
}, []);
```

---

## Using async/await

```jsx
useEffect(() => {
  async function loadData() {
    try {
      const response = await fetch("/api/users");
      const data = await response.json();
      console.log(data);
    } catch (error) {
      console.error(error);
    }
  }

  loadData();
}, []);
```

---

# 8.5 — Axios (Advanced Client)

Axios simplifies requests.

Install:

```bash
npm install axios
```

---

## GET with Axios

```jsx
import axios from "axios";

useEffect(() => {
  axios.get("/api/users")
    .then(res => console.log(res.data))
    .catch(err => console.error(err));
}, []);
```

---

## POST with Axios

```jsx
axios.post("/api/login", {
  email,
  password
});
```

Axios automatically:

* Converts JSON
* Handles response parsing
* Supports interceptors

---

# 8.6 — useEffect + API Calls (Correct Pattern)

Never write:

```jsx
useEffect(async () => { ... });
```

Correct pattern:

```jsx
useEffect(() => {
  async function fetchData() {
    const res = await fetch("/api/data");
    const data = await res.json();
  }

  fetchData();
}, []);
```

---

# 8.7 — Loading, Error, Success States

Think in terms of state machine:

```plaintext
idle → loading → success
        ↓
       error
```

---

## Example

```jsx
const [data, setData] = useState(null);
const [loading, setLoading] = useState(false);
const [error, setError] = useState(null);

useEffect(() => {
  async function fetchData() {
    setLoading(true);
    try {
      const res = await fetch("/api/data");
      const result = await res.json();
      setData(result);
    } catch (err) {
      setError(err);
    } finally {
      setLoading(false);
    }
  }

  fetchData();
}, []);
```

---

# 8.8 — Handling Race Conditions

Problem:
User types fast → multiple API calls → older response overrides newer.

Solution:

* AbortController
* Debouncing
* Track latest request

---

# 8.9 — Aborting Requests

```jsx
useEffect(() => {
  const controller = new AbortController();

  fetch("/api/data", { signal: controller.signal });

  return () => controller.abort();
}, []);
```

Prevents memory leaks.

---

# 8.10 — Authentication Flow (JWT)

Typical flow:

1. User submits credentials
2. Server verifies
3. Server returns JWT token
4. Client stores token
5. Client sends token in headers

---

## Sending Token

```jsx
axios.get("/api/profile", {
  headers: {
    Authorization: `Bearer ${token}`
  }
});
```

---

# 8.11 — Token Storage Strategies

### localStorage

* Simple
* Vulnerable to XSS

### httpOnly cookies

* More secure
* Cannot be accessed via JS
* Requires backend configuration

Production apps prefer httpOnly cookies.

---

# 8.12 — Role-Based Access

After login:

```javascript
{
  user: { role: "admin" }
}
```

Frontend can conditionally render:

```jsx
{user.role === "admin" && <AdminPanel />}
```

Backend must enforce security.

Frontend only controls UI visibility.

---

# 8.13 — Axios Interceptors

Used to:

* Attach token automatically
* Handle global errors

Example:

```jsx
axios.interceptors.request.use(config => {
  const token = localStorage.getItem("token");
  config.headers.Authorization = `Bearer ${token}`;
  return config;
});
```

---

# 8.14 — Global Error Handling

Example:

```jsx
axios.interceptors.response.use(
  res => res,
  err => {
    if (err.response.status === 401) {
      // logout user
    }
    return Promise.reject(err);
  }
);
```

---

# 8.15 — Custom API Hooks

Reusable logic:

```jsx
function useFetch(url) {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(setData);
  }, [url]);

  return data;
}
```

Usage:

```jsx
const users = useFetch("/api/users");
```

---

# 8.16 — File Upload

Using FormData:

```jsx
const formData = new FormData();
formData.append("file", file);

axios.post("/api/upload", formData);
```

---

# 8.17 — CORS (Deep Understanding)

CORS = Cross-Origin Resource Sharing.

Browser blocks requests if:

* Frontend and backend are on different origins
* Backend does not allow it

Backend must set headers:

```
Access-Control-Allow-Origin: *
```

---

# 8.18 — Optimistic UI Updates

Update UI before server responds.

Example:

```jsx
setTodos(prev => [...prev, newTodo]);
await axios.post("/api/todos", newTodo);
```

If fails:
Rollback change.

---

# 8.19 — Pagination Example

```jsx
axios.get(`/api/users?page=${page}&limit=10`);
```

UI controls page state.

---

# 8.20 — Common API Mistakes

1. Not handling loading state
2. Not handling errors
3. Storing token insecurely
4. Forgetting cleanup
5. Multiple unnecessary API calls
6. Storing server state globally without reason

---

# 📘 Integrated Production Example

```jsx
function Dashboard() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    async function loadUsers() {
      setLoading(true);
      try {
        const res = await axios.get("/api/users");
        setUsers(res.data);
      } catch (err) {
        console.error(err);
      } finally {
        setLoading(false);
      }
    }

    loadUsers();
  }, []);

  if (loading) return <p>Loading...</p>;

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

---

# 🧠 After Module 8 You Must Understand

* How to structure API calls
* Proper useEffect patterns
* Authentication flow
* Token handling
* Error handling strategies
* Interceptors
* Custom hooks for reuse
* Optimistic updates
* File uploads
* CORS basics

---

# 💡 Optional Exercises

1. Build login system with JWT.
2. Create paginated user list.
3. Implement global axios interceptor.
4. Build file upload component.
5. Create reusable useApi hook.

---


