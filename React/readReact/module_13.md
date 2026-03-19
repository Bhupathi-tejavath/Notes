
---

# 📘 MODULE 13 — Testing in React

(Unit Testing, Integration Testing, Mocking, Confidence Engineering)

---

# 📑 Module Structure

1. What is Testing?
2. Why Testing Matters in React Applications
3. Types of Testing (Deep)

   * Unit Testing
   * Integration Testing
   * End-to-End Testing
4. Testing Philosophy (What to Test & What NOT to Test)
5. Jest (Core Testing Engine)
6. React Testing Library (RTL)
7. Writing Your First Test
8. Query Methods in RTL (Deep)
9. Testing User Interactions
10. Testing Asynchronous Code
11. Mocking Functions
12. Mocking API Calls
13. Testing Forms
14. Testing Context
15. Testing Custom Hooks
16. Snapshot Testing
17. Code Coverage
18. Common Testing Mistakes
19. Integrated Example
20. Advanced Exercises

---

# 13.1 — What is Testing?

## Formal Definition

Testing is the process of verifying that a system behaves as expected under specific conditions and inputs.

In React:

Testing ensures:

* Components render correctly
* User interactions work
* State updates behave properly
* API integration doesn’t break UI
* Edge cases are handled

---

# 13.2 — Why Testing Matters

Without testing:

* Refactoring is risky
* Bugs reach production
* Complex UI becomes fragile
* Teams lose confidence

With testing:

* Safe refactoring
* Documentation of behavior
* Confidence in deployment
* Faster development long-term

---

# 13.3 — Types of Testing

---

## 13.3.1 — Unit Testing

Definition:
Testing a single unit (component, function, hook) in isolation.

Example:
Testing a Button renders correctly.

---

## 13.3.2 — Integration Testing

Definition:
Testing multiple components working together.

Example:
Form submission + validation + state update.

---

## 13.3.3 — End-to-End Testing (Conceptual)

Definition:
Testing full user flows in real browser.

Example:
Login → Dashboard → Logout.

Tools:

* Cypress
* Playwright

---

# 13.4 — Testing Philosophy

Important principle:

> Test behavior, not implementation.

Do NOT test:

* Internal state
* Hook usage details
* Implementation-specific DOM structure

Test:

* What user sees
* What user can do
* What user expects

---

# 13.5 — Jest (Testing Framework)

## Definition

Jest is a JavaScript testing framework used to run and organize tests.

It provides:

* Test runner
* Assertions
* Mocking system
* Snapshot testing
* Coverage reports

---

## Basic Jest Syntax

```javascript id="t0e8ea"
test("adds numbers", () => {
  expect(2 + 2).toBe(4);
});
```

---

## describe Block

```javascript id="sdue80"
describe("Math Tests", () => {
  test("adds", () => {
    expect(1 + 1).toBe(2);
  });
});
```

---

# 13.6 — React Testing Library (RTL)

## Definition

React Testing Library is a library for testing React components by simulating how users interact with them.

Philosophy:

> The more your tests resemble how your software is used, the more confidence they can give you.

---

# 13.7 — Writing Your First Component Test

Component:

```jsx id="mzhg8q"
function Button({ label }) {
  return <button>{label}</button>;
}
```

Test:

```javascript id="u7y4zr"
import { render, screen } from "@testing-library/react";

test("renders button text", () => {
  render(<Button label="Click Me" />);
  expect(screen.getByText("Click Me")).toBeInTheDocument();
});
```

---

# 13.8 — Query Methods (Deep Understanding)

RTL provides queries:

* getBy
* queryBy
* findBy

---

## getBy

Throws error if not found.

```javascript id="6t3qww"
screen.getByText("Submit");
```

---

## queryBy

Returns null if not found.

```javascript id="h7jra5"
screen.queryByText("Error");
```

---

## findBy (Async)

Used when element appears later.

```javascript id="8xk90g"
await screen.findByText("Loaded");
```

---

# 13.9 — Testing User Interactions

Using userEvent:

```javascript id="q2ft9z"
import userEvent from "@testing-library/user-event";

test("button click", async () => {
  render(<Counter />);
  const button = screen.getByRole("button");
  await userEvent.click(button);
});
```

---

# 13.10 — Testing Asynchronous Code

Component with API call:

```jsx id="5v4m6z"
useEffect(() => {
  fetchData().then(setData);
}, []);
```

Test:

```javascript id="j2d17p"
await waitFor(() => {
  expect(screen.getByText("Loaded")).toBeInTheDocument();
});
```

Or:

```javascript id="qv6ra4"
await screen.findByText("Loaded");
```

---

# 13.11 — Mocking Functions

Example:

```javascript id="ov7w2m"
const mockFn = jest.fn();
mockFn();
expect(mockFn).toHaveBeenCalled();
```

---

# 13.12 — Mocking API Calls

Using Jest:

```javascript id="9jvl2x"
global.fetch = jest.fn(() =>
  Promise.resolve({
    json: () => Promise.resolve({ data: "Test" })
  })
);
```

Or using axios mock adapter.

---

# 13.13 — Testing Forms

Example:

```javascript id="0z5u6m"
test("form submission", async () => {
  render(<LoginForm />);

  await userEvent.type(
    screen.getByLabelText("Email"),
    "test@example.com"
  );

  await userEvent.click(screen.getByText("Submit"));

  expect(screen.getByText("Success")).toBeInTheDocument();
});
```

Test behavior, not internal state.

---

# 13.14 — Testing Context

Wrap component in provider:

```javascript id="8r3qkz"
render(
  <AuthContext.Provider value={{ user: { name: "P" } }}>
    <Profile />
  </AuthContext.Provider>
);
```

---

# 13.15 — Testing Custom Hooks

Using renderHook:

```javascript id="d6ogrm"
import { renderHook } from "@testing-library/react";

const { result } = renderHook(() => useCounter());
expect(result.current.count).toBe(0);
```

---

# 13.16 — Snapshot Testing

Snapshot stores rendered output:

```javascript id="81iht3"
const { container } = render(<Button label="Test" />);
expect(container).toMatchSnapshot();
```

Used carefully.

Avoid overuse.

---

# 13.17 — Code Coverage

Jest can measure:

* Statements
* Branches
* Functions
* Lines

High coverage ≠ high quality.
Coverage ensures important paths tested.

---

# 13.18 — Common Testing Mistakes

1. Testing implementation details
2. Overusing snapshots
3. Mocking everything blindly
4. Ignoring async behavior
5. Not cleaning up mocks
6. Testing internal state

---

# 📘 Integrated Example

Component:

```jsx id="th74dp"
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <>
      <p>{count}</p>
      <button onClick={() => setCount(c => c + 1)}>
        Increment
      </button>
    </>
  );
}
```

Test:

```javascript id="a9dy5j"
test("increments count", async () => {
  render(<Counter />);

  const button = screen.getByText("Increment");

  await userEvent.click(button);

  expect(screen.getByText("1")).toBeInTheDocument();
});
```

---

# 🧠 After Module 13 You Must Understand

* Testing philosophy
* Jest fundamentals
* RTL queries
* Testing async UI
* Mocking APIs
* Testing forms & context
* Snapshot testing limits
* Coverage concepts
* Behavior-focused testing

---
