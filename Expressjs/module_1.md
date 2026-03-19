
---

# 📘 MODULE 1: Introduction to Express.js & Setup (Deep Dive)

---

## 1. What is Express.js?

### Definition

**Express.js** is a minimal and flexible web application framework built on top of **Node.js**. It provides a structured way to build:

* Web servers
* REST APIs
* Backend applications
* Full-stack web apps (with templating engines)

It abstracts lower-level HTTP handling provided by Node’s `http` module and provides:

* Routing system
* Middleware architecture
* Request & response utilities
* Simplified server configuration

---

### Why Express Exists

In pure Node.js:

```js
const http = require('http');

const server = http.createServer((req, res) => {
    if (req.url === '/home' && req.method === 'GET') {
        res.write('Home Page');
        res.end();
    }
});

server.listen(3000);
```

This quickly becomes messy as routes grow.

Express simplifies this:

```js
const express = require('express');
const app = express();

app.get('/home', (req, res) => {
    res.send('Home Page');
});

app.listen(3000);
```

Cleaner. Scalable. Modular.

---

### Core Philosophy

Express is:

* Unopinionated
* Middleware-driven
* Lightweight
* Modular

It does not force:

* ORM
* Folder structure
* Authentication method
* Templating engine

You choose architecture.

---

## 2. Express vs Node.js

### Node.js

* Runtime environment
* Executes JavaScript outside browser
* Provides core modules:

  * `http`
  * `fs`
  * `path`
  * `crypto`

### Express.js

* Framework built on Node
* Simplifies HTTP server development
* Adds:

  * Routing layer
  * Middleware chain
  * Error handling system

Think of it like:

> Node = Engine
> Express = Chassis + Controls

---

## 3. Express Architecture Overview

Express is built around:

### 1. Application Object

Created using:

```js
const app = express();
```

This `app` object:

* Registers middleware
* Registers routes
* Starts server

---

### 2. Middleware Stack

Every request passes through a stack of middleware functions.

```
Incoming Request
        ↓
Middleware 1
        ↓
Middleware 2
        ↓
Route Handler
        ↓
Response
```

---

### 3. Router Layer

Routes are defined based on:

* HTTP method
* URL path

Example:

```js
app.post('/login', handlerFunction);
```

---

### 4. Request-Response Lifecycle

1. Client sends HTTP request
2. Express matches route
3. Middleware executes
4. Route handler executes
5. Response is sent
6. Connection closes

---

## 4. Installing Node.js & npm

### Step 1: Install Node.js

Download from:
[https://nodejs.org](https://nodejs.org)

Verify installation:

```bash
node -v
npm -v
```

Node includes:

* Node runtime
* npm (Node Package Manager)

---

## 5. Creating a Node Project

### Initialize Project

```bash
npm init
```

Or quick version:

```bash
npm init -y
```

This creates:

```
package.json
```

---

### What is package.json?

It stores:

* Project name
* Version
* Scripts
* Dependencies
* Metadata

Example:

```json
{
  "name": "express-app",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "start": "node index.js"
  }
}
```

---

## 6. Installing Express

```bash
npm install express
```

This does:

* Downloads package
* Stores in `node_modules`
* Updates `package.json`
* Updates `package-lock.json`

Dependency appears as:

```json
"dependencies": {
  "express": "^4.18.2"
}
```

---

## 7. Folder Structure Conventions

### Basic Structure

```
project/
│
├── node_modules/
├── package.json
├── package-lock.json
├── index.js
```

---

### Professional Structure (Recommended)

```
project/
│
├── src/
│   ├── routes/
│   ├── controllers/
│   ├── models/
│   ├── middleware/
│   ├── config/
│   └── app.js
│
├── .env
├── package.json
└── server.js
```

We’ll cover architecture in later modules.

---

## 8. Creating First Express Server

Create `index.js`:

```js
const express = require('express');
const app = express();

app.get('/', (req, res) => {
    res.send('Hello World');
});

app.listen(3000, () => {
    console.log('Server running on port 3000');
});
```

---

### Breakdown

```js
const express = require('express');
```

Imports express module.

---

```js
const app = express();
```

Creates application instance.

---

```js
app.get('/', handler);
```

Registers route:

* Method: GET
* Path: /
* Handler: function

---

```js
app.listen(3000);
```

Starts server on port 3000.

---

## 9. Understanding app.listen()

### Syntax

```js
app.listen(port, callback)
```

Internally:

* Wraps Node's `http.createServer()`
* Binds server to TCP port
* Starts listening for connections

Example:

```js
app.listen(5000, () => {
    console.log('Server started on 5000');
});
```

---

### What is a Port?

Port = logical endpoint on server.

Common ports:

* 80 → HTTP
* 443 → HTTPS
* 3000 → Development

---

## 10. Understanding Request–Response Cycle

Let’s break deeply.

---

### Step 1: Client Sends Request

Example:

```
GET /home HTTP/1.1
Host: localhost:3000
```

---

### Step 2: Express Matches Route

Express checks:

* HTTP method
* URL path

---

### Step 3: Middleware Execution

If middleware exists:

```js
app.use((req, res, next) => {
    console.log('Middleware executed');
    next();
});
```

`next()` passes control.

---

### Step 4: Route Handler Executes

```js
app.get('/home', (req, res) => {
    res.send('Home Page');
});
```

---

### Step 5: Response Sent

`res.send()`:

* Sets status 200
* Sets content type
* Ends response

---

### Step 6: Connection Closed

TCP connection ends (unless keep-alive).

---

# 🧠 Important Internal Concepts

---

## Synchronous vs Asynchronous Behavior

Express handlers can be async:

```js
app.get('/data', async (req, res) => {
    const data = await fetchData();
    res.json(data);
});
```

Express does not block event loop.

---

## Event Loop Role

Node uses:

* Single-threaded event loop
* Non-blocking I/O

Express benefits from this for scalability.

---

# 🔎 Mini Practical Exercise

### Exercise 1

Create a server with:

* Route `/about`
* Route `/contact`
* Custom port 4000
* Console log request URL in middleware

---

### Exercise 2

Modify server to:

* Return JSON instead of text
* Return status 201

---

# 🎯 What You Should Understand After Module 1

You must clearly understand:

* What Express is and why it exists
* How it differs from Node
* How routing works at basic level
* How middleware flow works conceptually
* How request-response lifecycle functions
* How to initialize and structure project

---
