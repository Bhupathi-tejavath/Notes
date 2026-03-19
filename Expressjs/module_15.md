

---

# 📘 MODULE 15: Testing in Express (Unit & Integration Testing)

This module covers:

1. Why testing is critical
2. Types of testing
3. Unit testing vs integration testing
4. Test-driven development (TDD)
5. Setting up Jest
6. Writing unit tests
7. Mocking dependencies
8. Testing services
9. Testing controllers
10. Testing middleware
11. Integration testing with Supertest
12. Testing authentication routes
13. Test database strategy
14. Code coverage
15. CI/CD integration (intro)
16. Common mistakes
17. Exercises

---

# 1️⃣ Why Testing is Critical

Testing ensures:

✔ Refactoring doesn’t break features
✔ Business logic works
✔ Edge cases handled
✔ Security validation works
✔ Large systems remain stable

Without testing:

* Bugs appear in production
* Fear of modifying code
* Hard debugging

---

# 2️⃣ Types of Testing

---

## 1. Unit Testing

Tests a single function/module in isolation.

Example:

* Testing password hashing function
* Testing service method

---

## 2. Integration Testing

Tests multiple components working together.

Example:

* API endpoint + DB
* Auth middleware + route

---

## 3. End-to-End Testing

Tests full system from client to DB.

(Usually done with Cypress or Playwright.)

---

# 3️⃣ Unit vs Integration Testing

---

| Feature     | Unit Test       | Integration Test   |
| ----------- | --------------- | ------------------ |
| Speed       | Fast            | Slower             |
| Scope       | Single function | Full flow          |
| DB Access   | Usually mocked  | Often real         |
| Reliability | High isolation  | Realistic behavior |

You need both.

---

# 4️⃣ Test-Driven Development (TDD)

Process:

```plaintext
Write test → Run test (fail) → Write code → Test passes → Refactor
```

Benefits:

* Cleaner code
* Better design
* Fewer bugs

---

# 5️⃣ Setting Up Jest

Jest is the most popular JS testing framework.

---

## Install

```bash
npm install --save-dev jest
```

---

## Add to package.json

```json
"scripts": {
  "test": "jest"
}
```

---

## Run Tests

```bash
npm test
```

---

# 6️⃣ Writing Basic Unit Test

---

## Example Function

```js
// math.js
function add(a, b) {
    return a + b;
}

module.exports = add;
```

---

## Test File

```js
// math.test.js
const add = require('./math');

test('adds two numbers', () => {
    expect(add(2, 3)).toBe(5);
});
```

---

## Running Test

```bash
npm test
```

---

# 7️⃣ Testing Services (Best Practice)

You should primarily test:

✔ Services
✔ Utility functions
✔ Business logic

Example:

---

## user.service.js

```js
async function getUserById(repo, id) {
    const user = await repo.findById(id);
    if (!user) {
        throw new Error('User not found');
    }
    return user;
}

module.exports = { getUserById };
```

---

## user.service.test.js

```js
const { getUserById } = require('./user.service');

test('returns user if found', async () => {
    const mockRepo = {
        findById: jest.fn().mockResolvedValue({ id: 1 })
    };

    const user = await getUserById(mockRepo, 1);

    expect(user.id).toBe(1);
});
```

---

# 8️⃣ Mocking Dependencies

Mocking isolates logic from DB or external services.

---

## Mock Example

```js
jest.fn().mockResolvedValue(...)
```

This prevents actual DB call.

---

## Why Mock?

Without mock:

* Tests slow
* Requires DB
* Hard to isolate bugs

---

# 9️⃣ Testing Controllers

Controller example:

```js
async function getUsers(req, res) {
    const users = await userService.getUsers();
    res.json(users);
}
```

---

## Test Controller with Mocked Service

```js
test('controller returns users', async () => {
    const req = {};
    const res = {
        json: jest.fn()
    };

    const mockService = {
        getUsers: jest.fn().mockResolvedValue([{ id: 1 }])
    };

    await getUsers(req, res, mockService);

    expect(res.json).toHaveBeenCalledWith([{ id: 1 }]);
});
```

---

# 🔟 Testing Middleware

Example middleware:

```js
function protect(req, res, next) {
    if (!req.headers.authorization) {
        return res.status(401).json({ message: 'Unauthorized' });
    }
    next();
}
```

---

## Test Middleware

```js
test('returns 401 if no auth header', () => {
    const req = { headers: {} };
    const res = {
        status: jest.fn().mockReturnThis(),
        json: jest.fn()
    };
    const next = jest.fn();

    protect(req, res, next);

    expect(res.status).toHaveBeenCalledWith(401);
});
```

---

# 1️⃣1️⃣ Integration Testing with Supertest

Supertest allows testing API endpoints.

---

## Install

```bash
npm install --save-dev supertest
```

---

## Example

```js
const request = require('supertest');
const app = require('../app');

test('GET /users returns 200', async () => {
    const res = await request(app).get('/users');

    expect(res.statusCode).toBe(200);
});
```

---

# 1️⃣2️⃣ Testing Authentication Routes

Example:

```js
test('login returns token', async () => {
    const res = await request(app)
        .post('/login')
        .send({ email: 'test@test.com', password: '123456' });

    expect(res.body.token).toBeDefined();
});
```

---

# 1️⃣3️⃣ Test Database Strategy

---

## Option 1: Mock DB (Unit Testing)

Fast and safe.

---

## Option 2: Test Database

Use separate test DB:

```plaintext
college_test
```

Never use production DB in tests.

---

# 1️⃣4️⃣ Code Coverage

Jest can measure coverage.

Run:

```bash
npm test -- --coverage
```

Shows:

* % lines covered
* % functions covered

Goal:

✔ 70%+ coverage minimum
✔ 90%+ for core services

---

# 1️⃣5️⃣ CI/CD Integration (Intro)

In CI pipeline:

```plaintext
Push code → Run tests → If pass → Deploy
```

If tests fail → deployment blocked.

Tools:

* GitHub Actions
* GitLab CI
* Jenkins

---

# 1️⃣6️⃣ Common Mistakes

---

❌ Testing only routes, not services
❌ Not mocking external dependencies
❌ Using real production DB
❌ Writing brittle tests
❌ Not testing edge cases
❌ No coverage tracking

---

# 🧪 Exercises (Important for You)

---

### Exercise 1

Write unit test for:

```plaintext
StudentService.createStudent()
```

Mock repository.

---

### Exercise 2

Write integration test for:

```plaintext
POST /api/v1/students
```

Expect 201 response.

---

### Exercise 3

Test RBAC:

```plaintext
DELETE /users/:id
```

* Admin → 200
* Student → 403

---

# 🎯 After Module 15 You Must Master

* Unit testing with Jest
* Mocking dependencies
* Testing services
* Testing controllers
* Testing middleware
* Integration testing with Supertest
* Authentication route testing
* Code coverage basics
* Production reliability mindset

---

