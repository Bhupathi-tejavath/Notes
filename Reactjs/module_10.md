

# 📘 MODULE 10 — Forms & Advanced Input Handling

(Professional-Level Form Architecture)

---

# 📑 Module Structure

1. What is a Form in React?
2. Controlled Components (Deep)
3. Uncontrolled Components (Deep)
4. Controlled vs Uncontrolled (Architectural Comparison)
5. Handling Multiple Inputs
6. Form Submission Lifecycle
7. Validation Strategies
8. Synchronous Validation
9. Asynchronous Validation
10. Error State Architecture
11. Touched State & UX Patterns
12. Debouncing Inputs
13. Dynamic Forms
14. Field Arrays
15. File Upload Handling
16. Performance Issues in Forms
17. Form Libraries (React Hook Form – Conceptual Deep)
18. Schema Validation (Yup / Zod)
19. Accessibility in Forms
20. Security Considerations
21. Optimistic Form Submission
22. Resetting Forms Properly
23. Common Form Mistakes
24. Full Integrated Example
25. Advanced Exercises

---

# 10.1 — What is a Form in React?

## Formal Definition

A form in React is a structured collection of controlled or uncontrolled input elements that capture user data, validate it, manage its state, and submit it to a backend or internal logic for processing.

Forms are state-driven systems.

In React:

UI = f(formState)

Every input reflects internal state.

---

# 10.2 — Controlled Components (Deep Understanding)

## Definition

A controlled component is an input element whose value is controlled entirely by React state.

React is the single source of truth.

---

## Basic Example

```jsx
function NameInput() {
  const [name, setName] = useState("");

  return (
    <input
      value={name}
      onChange={(e) => setName(e.target.value)}
    />
  );
}
```

---

## Internal Flow

1. User types.
2. onChange triggers.
3. setName updates state.
4. Component re-renders.
5. Input value updated from state.

React controls the DOM.

---

## Why Controlled Components Matter

* Validation becomes easy
* Error handling predictable
* State synchronization guaranteed
* Business logic centralized

---

## When to Use Controlled

* Login forms
* Signup forms
* Search forms
* Any validation-heavy UI
* Complex dynamic forms

---

# 10.3 — Uncontrolled Components (Deep Understanding)

## Definition

An uncontrolled component stores its state inside the DOM rather than React state.

React accesses value via ref.

---

## Example

```jsx
function UncontrolledInput() {
  const inputRef = useRef();

  function handleSubmit() {
    console.log(inputRef.current.value);
  }

  return (
    <>
      <input ref={inputRef} />
      <button onClick={handleSubmit}>Submit</button>
    </>
  );
}
```

---

## Internal Behavior

DOM stores value.
React reads value only when needed.

---

## When to Use Uncontrolled

* Simple forms
* Performance-critical large forms
* File inputs (commonly)
* Integrating third-party libraries

---

# 10.4 — Controlled vs Uncontrolled (Architectural Comparison)

| Feature         | Controlled       | Uncontrolled     |
| --------------- | ---------------- | ---------------- |
| Source of truth | React state      | DOM              |
| Validation      | Easy             | Manual           |
| Re-render       | On every change  | Not required     |
| Predictability  | High             | Medium           |
| Performance     | Slightly heavier | Slightly lighter |

Professional apps mostly use controlled.

---

# 10.5 — Handling Multiple Inputs

Instead of separate states:

Bad:

```jsx
const [email, setEmail] = useState("");
const [password, setPassword] = useState("");
```

Better:

```jsx
const [form, setForm] = useState({
  email: "",
  password: ""
});
```

---

## Dynamic Change Handler

```jsx
function handleChange(e) {
  const { name, value } = e.target;
  setForm(prev => ({
    ...prev,
    [name]: value
  }));
}
```

Input:

```jsx
<input name="email" onChange={handleChange} />
```

---

# 10.6 — Form Submission Lifecycle

1. User fills form
2. Submit event triggered
3. preventDefault() called
4. Validate data
5. Send API request
6. Show loading state
7. Show success or error

---

## Example

```jsx
function LoginForm() {
  const [loading, setLoading] = useState(false);

  async function handleSubmit(e) {
    e.preventDefault();
    setLoading(true);

    try {
      await axios.post("/login", form);
    } catch (error) {
      console.error(error);
    } finally {
      setLoading(false);
    }
  }
}
```

---

# 10.7 — Validation Strategies

Validation ensures data correctness before submission.

Two types:

1. Synchronous validation
2. Asynchronous validation

---

# 10.8 — Synchronous Validation

Example:

```jsx
function validate(form) {
  const errors = {};

  if (!form.email.includes("@")) {
    errors.email = "Invalid email";
  }

  if (form.password.length < 6) {
    errors.password = "Password too short";
  }

  return errors;
}
```

---

# 10.9 — Asynchronous Validation

Used for:

* Checking if username exists
* Verifying email uniqueness

Example:

```jsx
async function validateUsername(username) {
  const res = await axios.get(`/check?username=${username}`);
  return res.data.available;
}
```

---

# 10.10 — Error State Architecture

Store errors in state:

```jsx
const [errors, setErrors] = useState({});
```

Display:

```jsx
{errors.email && <p>{errors.email}</p>}
```

---

# 10.11 — Touched State (Better UX)

Track whether user interacted with field.

```jsx
const [touched, setTouched] = useState({});
```

On blur:

```jsx
onBlur={() => setTouched(prev => ({ ...prev, email: true }))}
```

Show error only if touched.

---

# 10.12 — Debouncing Input

Used for search:

```jsx
useEffect(() => {
  const timer = setTimeout(() => {
    search(query);
  }, 500);

  return () => clearTimeout(timer);
}, [query]);
```

Prevents excessive API calls.

---

# 10.13 — Dynamic Forms

Form fields generated from configuration.

Example:

```jsx
const fields = [
  { name: "email", type: "text" },
  { name: "password", type: "password" }
];
```

Render dynamically:

```jsx
{fields.map(field => (
  <input
    key={field.name}
    name={field.name}
    type={field.type}
    onChange={handleChange}
  />
))}
```

---

# 10.14 — Field Arrays

Example:
Adding multiple skills dynamically.

```jsx
const [skills, setSkills] = useState([""]);
```

Add new:

```jsx
setSkills(prev => [...prev, ""]);
```

---

# 10.15 — File Upload Handling

File inputs are usually uncontrolled.

```jsx
function handleFile(e) {
  const file = e.target.files[0];
}
```

Using FormData:

```jsx
const formData = new FormData();
formData.append("file", file);

axios.post("/upload", formData);
```

---

# 10.16 — Performance Issues in Forms

Problems:

* Re-render on every keystroke
* Heavy validation
* Large dynamic forms

Solutions:

* useCallback
* useMemo
* Form libraries
* Uncontrolled inputs in large forms

---

# 10.17 — React Hook Form (Conceptual)

Benefits:

* Minimal re-renders
* Built-in validation
* Better performance

Example:

```jsx
import { useForm } from "react-hook-form";

const { register, handleSubmit } = useForm();

<input {...register("email")} />
```

Uses uncontrolled inputs internally.

---

# 10.18 — Schema Validation (Yup / Zod)

Example with Yup:

```jsx
const schema = yup.object({
  email: yup.string().email().required(),
  password: yup.string().min(6)
});
```

Schema ensures consistency.

---

# 10.19 — Accessibility in Forms

Important attributes:

```jsx
<label htmlFor="email">Email</label>
<input id="email" />
```

Add:

* aria-invalid
* aria-describedby

---

# 10.20 — Security Considerations

1. Never trust frontend validation.
2. Sanitize user input.
3. Avoid storing sensitive info.
4. Protect against XSS.
5. Use HTTPS.

---

# 10.21 — Optimistic Form Submission

Update UI before server responds:

```jsx
setTodos(prev => [...prev, newTodo]);
await axios.post("/todos", newTodo);
```

Rollback if failure.

---

# 10.22 — Resetting Forms

```jsx
setForm({
  email: "",
  password: ""
});
```

Or use HTML reset.

---

# 10.23 — Common Form Mistakes

1. No preventDefault
2. No loading state
3. No error handling
4. Storing derived errors
5. Over-validating on every keystroke
6. No accessibility attributes

---

# 📘 Full Integrated Form Example

```jsx
function RegisterForm() {
  const [form, setForm] = useState({
    email: "",
    password: ""
  });
  const [errors, setErrors] = useState({});
  const [loading, setLoading] = useState(false);

  function validate() {
    const newErrors = {};
    if (!form.email.includes("@"))
      newErrors.email = "Invalid email";
    if (form.password.length < 6)
      newErrors.password = "Too short";
    return newErrors;
  }

  async function handleSubmit(e) {
    e.preventDefault();

    const validationErrors = validate();
    if (Object.keys(validationErrors).length) {
      setErrors(validationErrors);
      return;
    }

    setLoading(true);

    try {
      await axios.post("/register", form);
      alert("Success");
    } catch (err) {
      console.error(err);
    } finally {
      setLoading(false);
    }
  }

  return (
    <form onSubmit={handleSubmit}>
      <input
        name="email"
        value={form.email}
        onChange={(e) =>
          setForm({ ...form, email: e.target.value })
        }
      />
      {errors.email && <p>{errors.email}</p>}

      <input
        type="password"
        name="password"
        value={form.password}
        onChange={(e) =>
          setForm({ ...form, password: e.target.value })
        }
      />
      {errors.password && <p>{errors.password}</p>}

      <button disabled={loading}>
        {loading ? "Loading..." : "Submit"}
      </button>
    </form>
  );
}
```

---

# 🧠 After Module 10 You Must Understand

* Controlled vs uncontrolled deeply
* Validation architecture
* Error management patterns
* Dynamic forms
* File uploads
* Performance in large forms
* Accessibility basics
* Security awareness
* Optimistic updates

---


