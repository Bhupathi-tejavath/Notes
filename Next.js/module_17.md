
---

# 📘 MODULE 17: Testing in Next.js


A production-ready system is not defined by features; it is defined by correctness under change. Testing ensures:

* Refactoring safety
* Regression prevention
* Stable deployments
* Developer confidence
* CI/CD automation

This module will cover:

* Unit testing
* Component testing
* Integration testing
* API route testing
* Server action testing
* End-to-End (E2E) testing
* Mocking strategies
* Testing architecture patterns



---

# 1. Testing Philosophy in Modern Web Applications

Testing in Next.js spans multiple layers:

1. **Unit Tests** → Test pure logic.
2. **Component Tests** → Test React components.
3. **Integration Tests** → Test multiple modules working together.
4. **API Tests** → Test route handlers.
5. **E2E Tests** → Simulate real user flows.

Testing pyramid principle:

* Many unit tests.
* Some integration tests.
* Fewer E2E tests.

This keeps test suite fast and maintainable.

---

# 2. Setting Up Jest for Unit & Component Testing

Install:

```bash
npm install --save-dev jest @testing-library/react @testing-library/jest-dom jest-environment-jsdom
```

Create `jest.config.js`:

```javascript
module.exports = {
  testEnvironment: "jsdom",
  moduleNameMapper: {
    "^@/(.*)$": "<rootDir>/$1",
  },
};
```

Add script in `package.json`:

```json
{
  "scripts": {
    "test": "jest"
  }
}
```

---

# 3. Unit Testing Pure Functions

Example domain function:

```ts
// domain/userService.ts
export function isValidEmail(email: string) {
  return email.includes("@");
}
```

Test:

```ts
import { isValidEmail } from "@/domain/userService";

test("valid email returns true", () => {
  expect(isValidEmail("test@example.com")).toBe(true);
});

test("invalid email returns false", () => {
  expect(isValidEmail("invalid")).toBe(false);
});
```

Unit tests should focus on:

* Business logic
* Validation logic
* Data transformation
* Utility functions

Avoid React or DB dependencies here.

---

# 4. Testing React Components

Example component:

```tsx
"use client";

export default function Button({ label }: { label: string }) {
  return <button>{label}</button>;
}
```

Test:

```tsx
import { render, screen } from "@testing-library/react";
import Button from "@/components/Button";

test("renders button label", () => {
  render(<Button label="Click Me" />);
  expect(screen.getByText("Click Me")).toBeInTheDocument();
});
```

Testing principles:

* Test behavior, not implementation.
* Avoid testing internal state directly.
* Prefer user-level interaction simulation.

---

# 5. Testing Client Interactions

Example:

```tsx
"use client";

import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);
  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  );
}
```

Test:

```tsx
import { render, screen, fireEvent } from "@testing-library/react";
import Counter from "@/components/Counter";

test("increments counter", () => {
  render(<Counter />);
  const button = screen.getByRole("button");
  fireEvent.click(button);
  expect(button).toHaveTextContent("1");
});
```

---

# 6. Testing Server Components

Server Components are harder to test directly because they execute on server.

Strategy:

* Extract logic into testable functions.
* Mock fetch.
* Use integration tests for rendering.

Example:

```ts
export async function fetchUsers() {
  const res = await fetch("https://api.example.com/users");
  return res.json();
}
```

Test:

```ts
global.fetch = jest.fn(() =>
  Promise.resolve({
    json: () => Promise.resolve([{ id: 1 }]),
  })
) as jest.Mock;

test("fetchUsers returns data", async () => {
  const data = await fetchUsers();
  expect(data.length).toBe(1);
});
```

---

# 7. Testing Route Handlers (API Testing)

Example route handler:

```ts
// app/api/hello/route.ts
export async function GET() {
  return Response.json({ message: "Hello" });
}
```

Test:

```ts
import { GET } from "@/app/api/hello/route";

test("GET returns hello message", async () => {
  const response = await GET();
  const json = await response.json();
  expect(json.message).toBe("Hello");
});
```

This tests API logic without running server.

---

# 8. Testing Server Actions

Extract business logic into domain layer.

Example:

```ts
// domain/postService.ts
export async function createPost(title: string) {
  return { id: 1, title };
}
```

Server Action calls domain function.

Test domain function separately.

Avoid tightly coupling tests to Server Action runtime.

---

# 9. Integration Testing

Integration tests verify multiple layers working together.

Example:

* Server Action + DB mock
* Route handler + validation
* Authentication middleware + route protection

Mock DB layer:

```ts
jest.mock("@/lib/db", () => ({
  insert: jest.fn(),
}));
```

Test interaction between modules.

---

# 10. End-to-End (E2E) Testing with Playwright

Install:

```bash
npm install --save-dev @playwright/test
```

Initialize:

```bash
npx playwright install
```

Example test:

```ts
import { test, expect } from "@playwright/test";

test("homepage loads", async ({ page }) => {
  await page.goto("http://localhost:3000");
  await expect(page.locator("h1")).toContainText("Welcome");
});
```

E2E tests simulate:

* User login
* Form submission
* Navigation
* Dashboard interactions

Run E2E against production build.

---

# 11. Testing Authentication

Example:

* Mock cookies.
* Mock JWT verification.
* Test middleware logic.

Middleware test:

```ts
import { middleware } from "@/middleware";
import { NextRequest } from "next/server";

test("redirects if no auth cookie", () => {
  const request = new NextRequest("http://localhost/dashboard");
  const response = middleware(request);
  expect(response.status).toBe(307);
});
```

---

# 12. Mocking Strategies

Use mocking for:

* External APIs
* Database
* Authentication
* Fetch
* Environment variables

Never test against real database in unit tests.

---

# 13. Test Environment Configuration

Use:

* `.env.test`
* Separate DB for test
* Mock services

Avoid using production secrets in test.

---

# 14. CI/CD Testing Pipeline

Typical pipeline:

1. Install dependencies.
2. Run unit tests.
3. Run integration tests.
4. Build project.
5. Run E2E tests.
6. Deploy only if tests pass.

This prevents broken deployments.

---

# 15. Coverage Reports

Generate coverage:

```bash
npm test -- --coverage
```

Coverage metrics:

* Statement coverage
* Branch coverage
* Function coverage

Aim for high coverage in domain logic, not necessarily UI markup.

---

# 16. Testing Best Practices

* Test behavior, not implementation details.
* Keep tests deterministic.
* Avoid flaky E2E tests.
* Keep test data minimal.
* Separate test concerns.
* Use realistic user flows.

---

# 17. Testing Anti-Patterns

Testing internal state directly
Over-mocking everything
Testing framework internals
Using real external APIs in tests
Skipping integration tests
No CI automation

---

# 18. Testing Strategy for Large SaaS

Layered approach:

Unit tests → domain layer
Component tests → UI
Integration tests → route + auth
E2E tests → login → dashboard → mutation

This ensures full-stack reliability.

---

# Understanding Check – Module 17

1. What is the difference between unit and integration testing?
2. Why should domain logic be separated for easier testing?
3. Why are E2E tests slower but important?
4. Why should external APIs be mocked in unit tests?
5. How can route handlers be tested without running server?
6. Why should CI block deployment if tests fail?
7. What is the purpose of coverage reports?

---


