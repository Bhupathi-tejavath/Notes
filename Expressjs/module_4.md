
---

# 📘 MODULE 4: Routing System (Advanced)

This module covers:

1. Why modular routing is necessary
2. Express Router object
3. Creating router instances
4. Mounting routers
5. Route grouping
6. Nested routes
7. Route parameters validation
8. Wildcard routes
9. Regex routes
10. Multiple route handlers
11. Route-specific middleware
12. API versioning strategies
13. Clean routing architecture
14. Common routing mistakes
15. Exercises

---

# 1️⃣ Why Modular Routing is Necessary

In small apps:

```js
app.get('/users', ...)
app.post('/users', ...)
app.get('/products', ...)
app.delete('/products/:id', ...)
```

In large apps:

* Hundreds of routes
* Different domains (users, auth, products, payments)
* Difficult to maintain
* Hard to debug

So we separate by responsibility.

---

# 2️⃣ Express Router Object

---

## Definition

`express.Router()` creates a **mini Express application**.

It has:

* Its own middleware
* Its own routes
* Its own stack

---

## Syntax

```js
const express = require('express');
const router = express.Router();
```

Think of Router as:

> Independent route module that can be mounted into main app.

---

# 3️⃣ Creating Router Instance

---

### users.routes.js

```js
const express = require('express');
const router = express.Router();

router.get('/', (req, res) => {
    res.send('All Users');
});

router.get('/:id', (req, res) => {
    res.send(`User ${req.params.id}`);
});

module.exports = router;
```

---

# 4️⃣ Mounting Routers

In `app.js`:

```js
const express = require('express');
const app = express();

const userRoutes = require('./users.routes');

app.use('/users', userRoutes);

app.listen(3000);
```

---

## Resulting Routes

| URL         | Handler            |
| ----------- | ------------------ |
| `/users`    | router.get('/')    |
| `/users/10` | router.get('/:id') |

---

## Internal Behavior

When request hits `/users/10`:

1. Express sees `/users`
2. Removes `/users`
3. Passes `/10` to router
4. Router matches `/:id`

---

# 5️⃣ Route Grouping

---

## Problem

Multiple related routes:

```js
router.get('/')
router.post('/')
router.put('/:id')
router.delete('/:id')
```

Better grouping:

```js
router.route('/')
    .get(getUsers)
    .post(createUser);

router.route('/:id')
    .get(getUserById)
    .put(updateUser)
    .delete(deleteUser);
```

---

## Benefits

* Cleaner syntax
* REST-style grouping
* Logical organization

---

# 6️⃣ Nested Routes

Used for hierarchical relationships.

Example:

```plaintext
Users → Posts
```

Routes:

```plaintext
/users/:userId/posts
/users/:userId/posts/:postId
```

---

### Implementation

```js
router.get('/:userId/posts', (req, res) => {
    res.send(`Posts of user ${req.params.userId}`);
});
```

---

## Important Concept: `mergeParams`

If using separate routers:

```js
const postRouter = express.Router({ mergeParams: true });
```

Without `mergeParams`, nested route params won't propagate.

---

# 7️⃣ Route Parameters Validation

Route parameters are strings.

If expecting number:

```js
router.get('/:id', (req, res) => {
    const id = Number(req.params.id);

    if (isNaN(id)) {
        return res.status(400).json({
            message: 'Invalid ID'
        });
    }

    res.send(`Valid ID ${id}`);
});
```

---

## Using Middleware for Validation

```js
function validateId(req, res, next) {
    if (!Number(req.params.id)) {
        return res.status(400).send('Invalid ID');
    }
    next();
}

router.get('/:id', validateId, handler);
```

---

# 8️⃣ Wildcard Routes

Used for catching unmatched routes.

```js
app.get('*', (req, res) => {
    res.status(404).send('Page Not Found');
});
```

Must be defined LAST.

---

# 9️⃣ Regex Routes

Express supports regular expressions.

---

### Example

```js
app.get(/^\/user\/(\d+)$/, (req, res) => {
    res.send('User ID only numbers allowed');
});
```

This matches:

```plaintext
/user/123
```

Not:

```plaintext
/user/abc
```

---

### Another Example

```js
app.get('/user/:id(\\d+)', (req, res) => {
    res.send('Only numeric ID');
});
```

Important: double escape `\\d+`.

---

# 🔟 Multiple Route Handlers

You can attach multiple middleware to a route.

```js
router.get('/dashboard',
    authenticate,
    authorize,
    (req, res) => {
        res.send('Dashboard');
    }
);
```

Execution:

```plaintext
authenticate → authorize → handler
```

---

# 1️⃣1️⃣ Route-Specific Middleware

Middleware applied only to specific route.

```js
router.get('/admin', adminCheck, handler);
```

---

# 1️⃣2️⃣ API Versioning

Professional APIs use versioning.

---

## Method 1: URL Versioning

```plaintext
/api/v1/users
/api/v2/users
```

Implementation:

```js
app.use('/api/v1', v1Routes);
app.use('/api/v2', v2Routes);
```

---

## Method 2: Header Versioning (Advanced)

Client sends:

```plaintext
Accept: application/vnd.myapp.v1+json
```

Server checks header.

---

## Why Versioning Matters

* Backward compatibility
* Safe updates
* Enterprise architecture

---

# 1️⃣3️⃣ Clean Routing Architecture

Recommended structure:

```
src/
├── routes/
│   ├── user.routes.js
│   ├── auth.routes.js
│   ├── product.routes.js
│
├── controllers/
│   ├── user.controller.js
│
├── middleware/
│
└── app.js
```

---

### Separation of Concerns

Route file:

```js
router.get('/', getUsers);
```

Controller file:

```js
function getUsers(req, res) {
    res.send('Users');
}
```

---

Benefits:

* Maintainability
* Scalability
* Testability

---

# 1️⃣4️⃣ Common Routing Mistakes

---

❌ Defining wildcard before routes

```js
app.get('*', handler);
app.get('/users', ...); // never reached
```

---

❌ Forgetting `mergeParams` in nested routes

---

❌ Duplicated routes

```js
app.get('/users', handler1);
app.get('/users', handler2); // only first runs
```

---

❌ Mixing business logic inside route file

Routes should delegate to controllers.

---

# 🧪 Exercises

---

### Exercise 1

Create modular routing for:

* `/api/v1/users`
* `/api/v1/products`

Separate route files.

---

### Exercise 2

Create nested routes:

```plaintext
/users/:id/orders
```

---

### Exercise 3

Create regex route that only accepts:

```plaintext
/order/6-digit-number
```

---

# 🎯 After Module 4 You Must Master

* How Express Router works internally
* How mounting modifies URL path
* Route grouping syntax
* Nested route parameter propagation
* Regex route constraints
* API versioning strategies
* Clean architecture routing

---


