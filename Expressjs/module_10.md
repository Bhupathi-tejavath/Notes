

---

# 📘 MODULE 10: Authentication & Authorization (Security Architecture Deep Dive)

This module covers:

1. Authentication vs Authorization
2. Session-based authentication
3. Cookies and session storage
4. JWT (JSON Web Token) authentication
5. Password hashing with bcrypt
6. Login & registration flow
7. Role-based access control (RBAC)
8. Middleware-based protection
9. Refresh tokens
10. Access token expiration strategies
11. Secure cookie practices
12. OAuth basics
13. Production security patterns
14. Common mistakes
15. Exercises

---

# 1️⃣ Authentication vs Authorization

---

## Authentication

> Verifies WHO the user is.

Example:

* Login with email + password
* Verify JWT

---

## Authorization

> Determines WHAT the user can access.

Example:

* Only admin can delete users
* Only faculty can approve certificates

---

# 2️⃣ Session-Based Authentication

Traditional method.

---

## How It Works

```plaintext
User logs in
      ↓
Server verifies credentials
      ↓
Server creates session
      ↓
Session ID stored in cookie
      ↓
Server stores session in memory/database
```

---

## Install express-session

```bash
npm install express-session
```

---

## Setup

```js
const session = require('express-session');

app.use(session({
    secret: 'mysecret',
    resave: false,
    saveUninitialized: false,
    cookie: {
        httpOnly: true
    }
}));
```

---

## Login Example

```js
app.post('/login', (req, res) => {
    const { email, password } = req.body;

    if (email === "admin@test.com") {
        req.session.user = { email };
        return res.send("Logged in");
    }

    res.status(401).send("Invalid credentials");
});
```

---

## Access Protected Route

```js
app.get('/dashboard', (req, res) => {
    if (!req.session.user) {
        return res.status(401).send("Unauthorized");
    }

    res.send("Dashboard");
});
```

---

## Problem with Sessions

* Requires server storage
* Hard to scale horizontally
* Session store needed (Redis)

---

# 3️⃣ Cookies

Cookies store small data in browser.

---

## Setting Cookie

```js
res.cookie('token', 'abc123', {
    httpOnly: true,
    secure: true,
    sameSite: 'strict'
});
```

---

## Important Flags

| Option   | Purpose               |
| -------- | --------------------- |
| httpOnly | Not accessible via JS |
| secure   | Only HTTPS            |
| sameSite | CSRF protection       |

---

# 4️⃣ JWT (JSON Web Token) Authentication

Most common in modern APIs.

---

## What is JWT?

A signed token containing payload.

Structure:

```plaintext
Header.Payload.Signature
```

Example:

```plaintext
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

---

## Install

```bash
npm install jsonwebtoken
```

---

## Creating Token

```js
const jwt = require('jsonwebtoken');

const token = jwt.sign(
    { userId: 1, role: 'admin' },
    'secretkey',
    { expiresIn: '1h' }
);
```

---

## Verifying Token

```js
const decoded = jwt.verify(token, 'secretkey');
```

---

## Login Flow (JWT)

```js
app.post('/login', async (req, res) => {
    const { email } = req.body;

    const token = jwt.sign(
        { email },
        process.env.JWT_SECRET,
        { expiresIn: '1h' }
    );

    res.json({ token });
});
```

---

## Protected Route Middleware

```js
function protect(req, res, next) {
    const authHeader = req.headers.authorization;

    if (!authHeader) {
        return res.status(401).json({ message: "No token" });
    }

    const token = authHeader.split(' ')[1];

    try {
        const decoded = jwt.verify(token, process.env.JWT_SECRET);
        req.user = decoded;
        next();
    } catch {
        res.status(401).json({ message: "Invalid token" });
    }
}
```

---

## Using Middleware

```js
app.get('/profile', protect, (req, res) => {
    res.json({ user: req.user });
});
```

---

# 5️⃣ Password Hashing with bcrypt

Never store plain passwords.

---

## Install

```bash
npm install bcrypt
```

---

## Hash Password

```js
const bcrypt = require('bcrypt');

const hashed = await bcrypt.hash(password, 10);
```

`10` = salt rounds.

---

## Compare Password

```js
const isMatch = await bcrypt.compare(password, user.password);
```

---

## Registration Example

```js
app.post('/register', async (req, res) => {
    const hashedPassword = await bcrypt.hash(req.body.password, 10);

    await User.create({
        email: req.body.email,
        password: hashedPassword
    });

    res.status(201).send("User created");
});
```

---

# 6️⃣ Role-Based Access Control (RBAC)

---

## Concept

Attach role to user:

```json
{
  "userId": 1,
  "role": "admin"
}
```

---

## Authorization Middleware

```js
function authorize(role) {
    return (req, res, next) => {
        if (req.user.role !== role) {
            return res.status(403).json({
                message: "Forbidden"
            });
        }
        next();
    };
}
```

---

## Usage

```js
app.delete('/users/:id',
    protect,
    authorize('admin'),
    deleteUser
);
```

---

# 7️⃣ Refresh Tokens

---

## Problem

Access tokens expire.

Solution:

* Short-lived access token
* Long-lived refresh token

---

## Flow

```plaintext
Login
 ↓
Access Token (15m)
Refresh Token (7d)
 ↓
When access expires → send refresh token
 ↓
Server issues new access token
```

---

## Best Practice

Store refresh token in:

* HttpOnly cookie
* Or secure DB store

---

# 8️⃣ Secure Token Storage

---

## Option 1: LocalStorage (Not recommended)

Vulnerable to XSS.

## Option 2: HttpOnly Cookie (Recommended)

Cannot be accessed via JS.

---

# 9️⃣ OAuth Basics

OAuth allows:

* Login via Google
* Login via GitHub

Flow:

```plaintext
User → Google → Google verifies → Google returns token → Your server verifies
```

Libraries:

* Passport.js
* OAuth2 clients

---

# 🔟 Production Security Patterns

---

✔ Store secrets in `.env`
✔ Use HTTPS only
✔ Use HttpOnly cookies
✔ Use short-lived tokens
✔ Implement rate limiting
✔ Hash passwords
✔ Validate input
✔ Avoid detailed error messages

---

# 1️⃣1️⃣ Common Mistakes

---

❌ Storing password in plain text
❌ Not hashing password
❌ Hardcoding JWT secret
❌ No expiration on tokens
❌ Not verifying token
❌ Allowing role manipulation from client

---

# 🧪 Exercises (Very Important for You)

---

### Exercise 1

Implement:

```plaintext
POST /register
POST /login
GET /profile
```

Using JWT.

---

### Exercise 2

Add roles:

* student
* faculty
* admin

Restrict:

```plaintext
DELETE /users/:id
```

To admin only.

---

### Exercise 3

Implement refresh token logic.

---

# 🎯 After Module 10 You Must Master

* Authentication vs authorization
* Session vs JWT differences
* Password hashing with bcrypt
* Middleware-based protection
* Role-based access control
* Token expiration strategy
* Secure cookie handling
* Production-level auth patterns

---
