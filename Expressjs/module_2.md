
---

# 📘 MODULE 2: Core Concepts of Express (Deep Dive)

This module covers:

1. `express()` function
2. Application object (`app`)
3. HTTP methods
4. Route handling basics
5. Route parameters
6. Query parameters
7. Sending responses
8. Status codes & response control

We will examine **definitions, syntax, internal behavior, examples, edge cases, and exercises**.

---

# 1️⃣ The `express()` Function

---

## Definition

`express()` is a **factory function** that creates an Express application instance.

```js
const express = require('express');
const app = express();
```

---

## What Happens Internally?

When you call:

```js
const app = express();
```

Express internally:

* Creates a function that acts as a request handler
* Attaches routing system
* Initializes middleware stack
* Attaches methods like `app.get()`, `app.post()`, etc.
* Wraps Node’s `http.createServer()` behavior

Internally it behaves like:

```js
function app(req, res, next) {
    // Handle request via middleware stack
}
```

That means the `app` object itself is technically a function.

---

## Key Property

```js
typeof app  // function
```

This is why Express can be passed directly to `http.createServer(app)`.

---

# 2️⃣ Application Object (`app`)

---

## Definition

The `app` object is the **central control unit** of your Express application.

It is responsible for:

* Registering routes
* Registering middleware
* Setting configurations
* Starting the server

---

## Common Methods on `app`

| Method         | Purpose                  |
| -------------- | ------------------------ |
| `app.get()`    | Handle GET requests      |
| `app.post()`   | Handle POST requests     |
| `app.put()`    | Handle PUT requests      |
| `app.delete()` | Handle DELETE requests   |
| `app.use()`    | Register middleware      |
| `app.listen()` | Start server             |
| `app.set()`    | Set configuration        |
| `app.engine()` | Register template engine |

---

# 3️⃣ HTTP Methods (REST Perspective)

HTTP methods represent the **intention of the request**.

---

## 3.1 GET

Used to **retrieve data**.

### Properties:

* Safe
* Idempotent
* No request body (usually)

Example:

```js
app.get('/users', (req, res) => {
    res.send('Get Users');
});
```

---

## 3.2 POST

Used to **create new resource**.

* Has request body
* Not idempotent

```js
app.post('/users', (req, res) => {
    res.send('Create User');
});
```

---

## 3.3 PUT

Used to **replace entire resource**.

```js
app.put('/users/1', (req, res) => {
    res.send('Update Entire User');
});
```

---

## 3.4 PATCH

Used to **partially update resource**.

```js
app.patch('/users/1', (req, res) => {
    res.send('Partial Update');
});
```

---

## 3.5 DELETE

Used to **remove resource**.

```js
app.delete('/users/1', (req, res) => {
    res.send('Delete User');
});
```

---

## How Express Matches Methods

Express checks:

1. Request method
2. Route path

Both must match.

---

# 4️⃣ Route Handling Basics

---

## General Syntax

```js
app.METHOD(PATH, HANDLER)
```

Example:

```js
app.get('/home', (req, res) => {
    res.send('Home');
});
```

---

## Handler Function Structure

```js
(req, res) => { }
```

### `req` → Incoming request

### `res` → Outgoing response

---

## Multiple Handlers

```js
app.get('/home',
    (req, res, next) => {
        console.log('First');
        next();
    },
    (req, res) => {
        res.send('Second');
    }
);
```

---

# 5️⃣ Route Parameters

---

## Definition

Route parameters allow dynamic URL segments.

Example URL:

```
/users/25
```

Route:

```js
app.get('/users/:id', (req, res) => {
    res.send(req.params.id);
});
```

---

## How It Works

`:id` becomes a key in:

```js
req.params
```

Example:

```js
req.params = { id: '25' }
```

---

## Multiple Parameters

```js
app.get('/users/:id/posts/:postId', (req, res) => {
    res.json(req.params);
});
```

Request:

```
/users/10/posts/5
```

Output:

```json
{
  "id": "10",
  "postId": "5"
}
```

---

## Important Notes

* Route params are always strings.
* Order matters.
* `/users/:id` ≠ `/users/:userId`

---

# 6️⃣ Query Parameters

---

## Definition

Query parameters are key-value pairs after `?`.

Example:

```
/search?keyword=node&page=2
```

---

## Accessing Query Params

```js
app.get('/search', (req, res) => {
    res.json(req.query);
});
```

Result:

```json
{
  "keyword": "node",
  "page": "2"
}
```

---

## Differences: Route Params vs Query Params

| Route Param           | Query Param      |
| --------------------- | ---------------- |
| Part of URL structure | Optional filters |
| Required for match    | Optional         |
| `/users/:id`          | `/users?id=1`    |

---

# 7️⃣ Sending Responses

Express extends Node’s response object.

---

## 7.1 `res.send()`

Sends:

* String
* Object
* Buffer
* Array

```js
res.send('Hello');
res.send({ name: 'Pradeep' });
```

If object → automatically converts to JSON.

---

## 7.2 `res.json()`

Always sends JSON.

```js
res.json({ success: true });
```

Sets header:

```
Content-Type: application/json
```

---

## 7.3 `res.status()`

Sets HTTP status code.

```js
res.status(404).send('Not Found');
```

Chaining works because methods return `res`.

---

## 7.4 `res.redirect()`

Redirects client.

```js
res.redirect('/login');
```

Default status: 302

---

## 7.5 `res.sendFile()`

Sends file from server.

```js
const path = require('path');

res.sendFile(path.join(__dirname, 'index.html'));
```

---

## 7.6 `res.end()`

Ends response manually.

Rarely used in Express.

---

# 8️⃣ Response Status Codes (REST-Oriented)

---

## 200 Series (Success)

* 200 OK
* 201 Created
* 204 No Content

Example:

```js
res.status(201).json({ message: 'Created' });
```

---

## 400 Series (Client Error)

* 400 Bad Request
* 401 Unauthorized
* 403 Forbidden
* 404 Not Found

---

## 500 Series (Server Error)

* 500 Internal Server Error

---

## Professional Pattern

```js
if (!user) {
    return res.status(404).json({
        success: false,
        message: 'User not found'
    });
}
```

Always return after sending response to prevent double send.

---

# ⚠️ Common Beginner Mistakes

---

### ❌ Sending Multiple Responses

```js
res.send('Hello');
res.send('World'); // ERROR
```

---

### ❌ Forgetting return

```js
if (!user) {
    res.status(404).send('Not found');
}
// Code continues executing
```

Correct:

```js
if (!user) {
    return res.status(404).send('Not found');
}
```

---

# 🔎 Mini Practice Tasks

---

### Exercise 1

Create routes:

* GET `/products`
* GET `/products/:id`
* POST `/products`
* DELETE `/products/:id`

Return JSON responses.

---

### Exercise 2

Create search endpoint:

```
/books?author=xyz&year=2023
```

Return query parameters in JSON.

---

### Exercise 3

Return:

* 201 on creation
* 404 if ID < 1
* 400 if missing data

---

# 🎯 After Module 2 You Must Clearly Understand

* How Express matches routes
* How HTTP methods differ
* How dynamic routing works
* How query parameters differ from route parameters
* How response methods function internally
* How to correctly set status codes
* How to avoid common routing mistakes

---


