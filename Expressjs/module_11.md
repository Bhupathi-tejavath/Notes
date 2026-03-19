

---

# 📘 MODULE 11: Error Handling & Debugging (Production-Grade Stability)

This module covers:

1. What is an error in backend systems?
2. Types of errors
3. Express default error handling
4. Custom error-handling middleware
5. Centralized error architecture
6. Async error handling
7. Custom error classes
8. Operational vs programming errors
9. Logging (Morgan)
10. Advanced logging (Winston)
11. Debugging strategies
12. Production vs development error responses
13. Global process-level error handling
14. Common mistakes
15. Exercises

---

# 1️⃣ What is an Error in Backend Systems?

An error is any unexpected condition that prevents normal execution.

Examples:

* Invalid user input
* Database connection failure
* Invalid JWT
* Undefined variable
* JSON parse failure

Errors must be handled gracefully — not crash the server.

---

# 2️⃣ Types of Errors

---

## 1. Operational Errors (Expected)

Examples:

* Invalid credentials
* Resource not found
* Validation failure

These should be handled and returned to client properly.

---

## 2. Programming Errors (Unexpected)

Examples:

```js
undefinedVariable.method();
```

* Syntax errors
* Logic bugs
* Undefined variables

These should be logged and fixed — not shown to user.

---

# 3️⃣ Express Default Error Handling

If you throw an error:

```js
app.get('/error', (req, res) => {
    throw new Error('Something broke');
});
```

Express:

* Catches it
* Sends HTML stack trace (in development)
* Sends minimal error (in production)

But this is not structured.

We need centralized error system.

---

# 4️⃣ Custom Error-Handling Middleware

Error middleware must have 4 parameters:

```js
function (err, req, res, next)
```

---

## Example

```js
app.use((err, req, res, next) => {
    console.error(err.stack);

    res.status(500).json({
        success: false,
        message: err.message
    });
});
```

⚠ Must be defined AFTER all routes.

---

# 5️⃣ Centralized Error Architecture

Instead of:

```js
res.status(404).json({ message: 'User not found' });
```

We throw error:

```js
throw new Error('User not found');
```

And let centralized middleware handle response.

---

# 6️⃣ Async Error Handling (Critical Concept)

Express does NOT automatically catch async errors.

---

## Problem Example

```js
app.get('/data', async (req, res) => {
    throw new Error('Async error');
});
```

Server may crash.

---

## Solution: Try-Catch

```js
app.get('/data', async (req, res, next) => {
    try {
        throw new Error('Error');
    } catch (err) {
        next(err);
    }
});
```

---

## Better Solution: Async Wrapper

```js
const asyncHandler = fn =>
    (req, res, next) =>
        Promise.resolve(fn(req, res, next)).catch(next);
```

Usage:

```js
app.get('/data', asyncHandler(async (req, res) => {
    throw new Error('Handled error');
}));
```

This is professional pattern.

---

# 7️⃣ Custom Error Class (Production Pattern)

Instead of generic Error, create structured error.

---

## Create Error Class

```js
class AppError extends Error {
    constructor(message, statusCode) {
        super(message);
        this.statusCode = statusCode;
        this.isOperational = true;

        Error.captureStackTrace(this, this.constructor);
    }
}
```

---

## Throw Custom Error

```js
if (!user) {
    return next(new AppError('User not found', 404));
}
```

---

## Error Middleware Handling

```js
app.use((err, req, res, next) => {
    const statusCode = err.statusCode || 500;

    res.status(statusCode).json({
        success: false,
        message: err.message
    });
});
```

---

# 8️⃣ Development vs Production Error Response

---

## Development Mode

Show stack trace.

```js
if (process.env.NODE_ENV === 'development') {
    res.status(statusCode).json({
        message: err.message,
        stack: err.stack
    });
}
```

---

## Production Mode

Hide stack trace.

```js
if (process.env.NODE_ENV === 'production') {
    res.status(statusCode).json({
        message: err.isOperational
            ? err.message
            : 'Something went wrong'
    });
}
```

---

# 9️⃣ Logging with Morgan

---

## Install

```bash
npm install morgan
```

---

## Usage

```js
const morgan = require('morgan');

app.use(morgan('dev'));
```

Outputs:

```plaintext
GET /users 200 5.342 ms
```

---

# 🔟 Advanced Logging with Winston

---

## Install

```bash
npm install winston
```

---

## Setup Logger

```js
const winston = require('winston');

const logger = winston.createLogger({
    level: 'info',
    transports: [
        new winston.transports.Console(),
        new winston.transports.File({ filename: 'errors.log' })
    ]
});
```

---

## Log Error

```js
logger.error(err.message);
```

Better for production systems.

---

# 1️⃣1️⃣ Debugging Strategies

---

## 1. Console Logging

Use structured logging.

---

## 2. Node Debug Mode

```bash
node --inspect server.js
```

Use Chrome DevTools.

---

## 3. Nodemon

```bash
npm install nodemon --save-dev
```

Auto-restarts server.

---

## 4. Use Proper HTTP Clients

* Postman
* Thunder Client
* Curl

---

# 1️⃣2️⃣ Handling Uncaught Exceptions

---

## Uncaught Exception

```js
process.on('uncaughtException', err => {
    console.log('Uncaught Exception:', err);
    process.exit(1);
});
```

---

## Unhandled Promise Rejection

```js
process.on('unhandledRejection', err => {
    console.log('Unhandled Rejection:', err);
    process.exit(1);
});
```

Used for production safety.

---

# 1️⃣3️⃣ Production-Grade Error Flow

```plaintext
Request
  ↓
Controller
  ↓
If error → next(err)
  ↓
Central Error Middleware
  ↓
Log error
  ↓
Send structured JSON
```

---

# 1️⃣4️⃣ Common Mistakes

---

❌ Forgetting `next(err)`
❌ Not handling async errors
❌ Exposing stack traces in production
❌ Logging sensitive data
❌ No centralized error middleware
❌ Using 200 for errors

---

# 🧪 Exercises

---

### Exercise 1

Create custom error class.

Throw:

```plaintext
404 if user not found
400 if invalid input
```

---

### Exercise 2

Implement asyncHandler wrapper.

Use it in all controllers.

---

### Exercise 3

Configure:

* Morgan for request logs
* Winston for error logs

---

# 🎯 After Module 11 You Must Master

* Difference between operational & programming errors
* Centralized error handling
* Async error management
* Custom error classes
* Development vs production error responses
* Logging architecture
* Process-level crash handling

---


