

---

# 📘 MODULE 3: Middleware Deep Dive

We will cover:

1. What is middleware?
2. Middleware execution model
3. `next()` function mechanics
4. Types of middleware
5. Application-level middleware
6. Router-level middleware
7. Built-in middleware
8. Third-party middleware
9. Writing custom middleware
10. Middleware chaining
11. Order of execution
12. Error-handling middleware
13. Async middleware
14. Real-world architecture patterns
15. Common mistakes
16. Exercises

---

# 1️⃣ What is Middleware?

---

## Definition

Middleware is a function that has access to:

* `req` (request object)
* `res` (response object)
* `next` (function that passes control to next middleware)

### Syntax

```js
function middleware(req, res, next) {
    // logic
    next();
}
```

Or arrow function:

```js
(req, res, next) => {
    next();
}
```

---

## Conceptual Meaning

Middleware is a **processing layer** that sits between:

> Incoming Request → Route Handler → Response

It can:

* Modify request
* Modify response
* End response
* Pass control
* Throw error

---

## Real-world Analogy

Think of airport security:

```
Passenger → ID Check → Baggage Scan → Boarding Gate
```

Each checkpoint = middleware.

---

# 2️⃣ Middleware Execution Model

Express maintains an **internal stack (array)** of middleware functions.

When request arrives:

```
Stack[0] → Stack[1] → Stack[2] → ...
```

Each middleware decides:

* Call `next()` → Continue
* Send response → Stop
* Throw error → Go to error handler

---

# 3️⃣ The `next()` Function

---

## Definition

`next()` tells Express:

> "Move to the next middleware in the stack."

If `next()` is NOT called:

* Request hangs
* No response
* Client waits forever

---

## Example

```js
app.use((req, res, next) => {
    console.log('First middleware');
    next();
});

app.get('/', (req, res) => {
    res.send('Home');
});
```

Flow:

1. Middleware logs
2. `next()` called
3. Route handler runs

---

## Passing Errors

```js
next(new Error('Something went wrong'));
```

This skips normal middleware and jumps to error middleware.

---

# 4️⃣ Types of Middleware

Express supports five types:

1. Application-level
2. Router-level
3. Built-in
4. Third-party
5. Error-handling

We examine each deeply.

---

# 5️⃣ Application-Level Middleware

---

## Definition

Middleware attached directly to the app instance.

### Syntax

```js
app.use(middlewareFunction);
```

---

## Example: Logger

```js
app.use((req, res, next) => {
    console.log(`${req.method} ${req.url}`);
    next();
});
```

This runs for **every request**.

---

## Mount Path Usage

```js
app.use('/api', middleware);
```

Now middleware runs only for routes starting with `/api`.

---

# 6️⃣ Router-Level Middleware

Used with Express Router.

```js
const router = express.Router();

router.use((req, res, next) => {
    console.log('Router middleware');
    next();
});
```

Mounted as:

```js
app.use('/users', router);
```

Runs only for `/users` routes.

---

# 7️⃣ Built-in Middleware

Express provides built-in middleware.

---

## 7.1 `express.json()`

Parses JSON request body.

```js
app.use(express.json());
```

Without this:

```js
req.body // undefined
```

---

## 7.2 `express.urlencoded()`

Parses form data.

```js
app.use(express.urlencoded({ extended: true }));
```

---

## 7.3 `express.static()`

Serves static files.

```js
app.use(express.static('public'));
```

If `public/index.html` exists:

```
GET /
```

Serves that file automatically.

---

# 8️⃣ Third-Party Middleware

Installed via npm.

Example:

```bash
npm install morgan
```

Usage:

```js
const morgan = require('morgan');
app.use(morgan('dev'));
```

Common third-party middleware:

* Morgan (logging)
* Helmet (security)
* CORS
* Multer (file upload)

---

# 9️⃣ Writing Custom Middleware

---

## Example: Authentication Check

```js
function authMiddleware(req, res, next) {
    const token = req.headers.authorization;

    if (!token) {
        return res.status(401).json({
            message: 'Unauthorized'
        });
    }

    next();
}
```

Use:

```js
app.get('/dashboard', authMiddleware, (req, res) => {
    res.send('Dashboard');
});
```

---

## Middleware That Modifies Request

```js
app.use((req, res, next) => {
    req.customProperty = "Injected Data";
    next();
});
```

Now available in all routes.

---

# 🔟 Middleware Chaining

You can chain multiple middleware.

```js
app.get('/data',
    middleware1,
    middleware2,
    (req, res) => {
        res.send('Final Handler');
    }
);
```

Execution order:

```
middleware1 → middleware2 → route handler
```

---

# 1️⃣1️⃣ Order of Execution (Critical Concept)

Middleware order matters.

---

## Example 1 (Correct Order)

```js
app.use(express.json());
app.post('/data', handler);
```

---

## Example 2 (Wrong Order)

```js
app.post('/data', handler);
app.use(express.json());
```

Now `req.body` will not work.

---

## Rule

Express executes middleware in the order they are declared.

---

# 1️⃣2️⃣ Error-Handling Middleware

---

## Definition

Error middleware has 4 parameters:

```js
function(err, req, res, next)
```

Note: 4 parameters are mandatory.

---

## Example

```js
app.use((err, req, res, next) => {
    console.error(err.message);
    res.status(500).json({
        message: 'Server Error'
    });
});
```

---

## Throwing Error in Route

```js
app.get('/error', (req, res) => {
    throw new Error('Something broke');
});
```

Express catches and forwards to error middleware.

---

# 1️⃣3️⃣ Async Middleware (Very Important)

Express does not automatically catch async errors.

Wrong:

```js
app.get('/data', async (req, res) => {
    throw new Error('Async error');
});
```

This may crash server.

---

Correct approach:

```js
app.get('/data', async (req, res, next) => {
    try {
        throw new Error('Async error');
    } catch (err) {
        next(err);
    }
});
```

---

Professional Pattern: Async Wrapper

```js
const asyncHandler = fn => (req, res, next) =>
    Promise.resolve(fn(req, res, next)).catch(next);
```

Usage:

```js
app.get('/data', asyncHandler(async (req, res) => {
    throw new Error('Error');
}));
```

---

# 1️⃣4️⃣ Real-World Middleware Patterns

---

## Logger

Logs request details.

## Auth

Validates user token.

## Role-Based Access

Checks user role before route.

## Input Validator

Validates request body.

## Rate Limiter

Prevents abuse.

---

# 1️⃣5️⃣ Common Mistakes

---

❌ Forgetting `next()`

❌ Sending response and calling `next()`

```js
res.send('Done');
next(); // wrong
```

❌ Defining error middleware before routes

Error middleware must be last.

---

# 1️⃣6️⃣ Execution Flow Visualization

Given:

```js
app.use(m1);
app.use(m2);
app.get('/test', m3, handler);
app.use(errorHandler);
```

Flow:

```
Request →
m1 →
m2 →
m3 →
handler →
Response
```

If error occurs:

```
Request →
m1 →
m2 →
m3 →
Error →
errorHandler
```

---

# 🧪 Exercises

---

### Exercise 1

Create global logger middleware that prints:

```
[GET] /users at 10:30 AM
```

---

### Exercise 2

Create authentication middleware:

* If header `x-api-key` missing → 403
* Else → allow request

---

### Exercise 3

Create error handler that returns:

```json
{
  "success": false,
  "error": "message"
}
```

---

# 🎯 After Module 3 You Must Master

* What middleware really is
* How Express internally executes middleware stack
* How `next()` works
* How to build custom middleware
* Why order matters
* How error-handling works
* How to manage async middleware

---

