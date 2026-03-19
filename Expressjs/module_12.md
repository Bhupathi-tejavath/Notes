

---

# 📘 MODULE 12: Security in Express (Hardening Your Backend)

This module covers:

1. Why backend security matters
2. Common attack categories
3. Helmet (secure HTTP headers)
4. CORS configuration
5. XSS (Cross-Site Scripting) prevention
6. CSRF (Cross-Site Request Forgery) prevention
7. Rate limiting
8. Data sanitization
9. SQL Injection prevention
10. Environment variables & secrets management
11. Secure cookies
12. HTTPS enforcement
13. Security headers
14. Production security checklist
15. Common mistakes
16. Exercises

---

# 1️⃣ Why Backend Security Matters

Your backend:

* Stores user credentials
* Handles authentication tokens
* Manages personal data
* Executes database queries

If compromised:

* Data leaks
* Account takeover
* System manipulation
* Legal consequences

Security must be layered.

---

# 2️⃣ Common Attack Categories

---

## 1. XSS (Cross-Site Scripting)

Injecting malicious script into pages.

---

## 2. CSRF

Tricking user into performing unwanted action.

---

## 3. SQL Injection

Injecting malicious SQL queries.

---

## 4. Brute Force

Repeated login attempts.

---

## 5. Token theft

Stealing JWT or session ID.

---

## 6. DOS (Denial of Service)

Flooding server with requests.

---

# 3️⃣ Helmet – Secure HTTP Headers

Helmet sets various HTTP security headers automatically.

---

## Install

```bash id="hsr9i9"
npm install helmet
```

---

## Setup

```js id="8n6vlj"
const helmet = require('helmet');

app.use(helmet());
```

---

## What Helmet Sets

| Header                    | Purpose               |
| ------------------------- | --------------------- |
| X-Content-Type-Options    | Prevent MIME sniffing |
| X-Frame-Options           | Prevent clickjacking  |
| Content-Security-Policy   | Prevent XSS           |
| Strict-Transport-Security | Force HTTPS           |

---

## Custom Helmet Config

```js id="qt3zqe"
app.use(helmet({
    contentSecurityPolicy: false
}));
```

---

# 4️⃣ CORS (Cross-Origin Resource Sharing)

Controls which domains can access your API.

---

## Problem

Frontend running on:

```plaintext id="7kpxsb"
http://localhost:3001
```

Backend on:

```plaintext id="1k6kq3"
http://localhost:3000
```

Browser blocks request without CORS config.

---

## Install

```bash id="u9gjmd"
npm install cors
```

---

## Basic Setup

```js id="i7rfsq"
const cors = require('cors');

app.use(cors());
```

---

## Restrict Specific Origin

```js id="6pbo8p"
app.use(cors({
    origin: 'http://localhost:3001'
}));
```

---

## Allow Credentials

```js id="pt6vwn"
app.use(cors({
    origin: 'http://localhost:3001',
    credentials: true
}));
```

---

# 5️⃣ XSS (Cross-Site Scripting)

---

## What is XSS?

Attacker injects:

```html id="h9e17u"
<script>alert('Hacked')</script>
```

If rendered without escaping → malicious code runs.

---

## Prevention

### ✔ Escape user input

### ✔ Use Helmet

### ✔ Use Content Security Policy

### ✔ Avoid `<%- %>` in EJS for user data

---

## Use xss-clean (Deprecated but conceptually important)

```bash id="1e6oz0"
npm install xss-clean
```

---

Better modern practice: validate + sanitize input manually.

---

# 6️⃣ CSRF Protection

---

## What is CSRF?

User logged in → attacker tricks them into sending request like:

```plaintext id="q5n04m"
POST /transfer-money
```

Without their knowledge.

---

## Install CSRF Middleware

```bash id="on3l7f"
npm install csurf
```

---

## Setup

```js id="r4szwn"
const csrf = require('csurf');

app.use(csrf({ cookie: true }));
```

---

## Usage in Form

```html id="q5dxu4"
<input type="hidden" name="_csrf" value="<%= csrfToken %>">
```

---

CSRF mostly needed for session-based apps.

JWT-based APIs typically not vulnerable if stored properly.

---

# 7️⃣ Rate Limiting

Prevents brute-force & DOS.

---

## Install

```bash id="apdu16"
npm install express-rate-limit
```

---

## Setup

```js id="nchb61"
const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
    windowMs: 15 * 60 * 1000,
    max: 100
});

app.use(limiter);
```

Limits 100 requests per 15 minutes per IP.

---

## Protect Login Route Only

```js id="7t59yl"
app.use('/login', loginLimiter);
```

---

# 8️⃣ Data Sanitization

---

## SQL Injection Prevention

Never do:

```js id="j79w1v"
const query = `SELECT * FROM users WHERE id = ${req.params.id}`;
```

---

Always use parameterized query:

```js id="r8nqto"
pool.query(
    'SELECT * FROM users WHERE id = ?',
    [req.params.id]
);
```

---

## MongoDB Injection Prevention

Avoid:

```js id="y6txok"
User.find(req.body);
```

Whitelist fields explicitly.

---

# 9️⃣ Environment Variables & Secrets

Never hardcode:

```js id="m8xgdf"
const JWT_SECRET = "supersecret";
```

---

## Use dotenv

```bash id="6lgp0x"
npm install dotenv
```

---

## Setup

```js id="v4nq5i"
require('dotenv').config();

const secret = process.env.JWT_SECRET;
```

---

## .env File

```plaintext id="5eh0ek"
JWT_SECRET=mySuperSecretKey
DB_PASSWORD=abc123
```

---

Never commit `.env` to GitHub.

---

# 🔟 Secure Cookies

---

## Best Configuration

```js id="a9j60h"
res.cookie('token', token, {
    httpOnly: true,
    secure: true,
    sameSite: 'strict'
});
```

---

## Explanation

| Option   | Purpose           |
| -------- | ----------------- |
| httpOnly | Prevent JS access |
| secure   | HTTPS only        |
| sameSite | Prevent CSRF      |

---

# 1️⃣1️⃣ HTTPS Enforcement

Force HTTPS in production.

```js id="px9j6o"
app.use((req, res, next) => {
    if (req.headers['x-forwarded-proto'] !== 'https') {
        return res.redirect(`https://${req.headers.host}${req.url}`);
    }
    next();
});
```

---

# 1️⃣2️⃣ Security Headers Manually

---

## Disable X-Powered-By

```js id="hgn57k"
app.disable('x-powered-by');
```

Prevents exposing Express.

---

## Set Custom Header

```js id="tx9x5l"
res.set('X-Content-Type-Options', 'nosniff');
```

---

# 1️⃣3️⃣ Production Security Checklist

---

✔ Use Helmet
✔ Use CORS properly
✔ Use HTTPS
✔ Use JWT expiration
✔ Hash passwords
✔ Validate input
✔ Sanitize input
✔ Use rate limiting
✔ Disable x-powered-by
✔ Use environment variables
✔ Restrict file upload types
✔ Limit JSON payload size
✔ Handle errors properly
✔ Keep dependencies updated

---

# 1️⃣4️⃣ Common Mistakes

---

❌ Storing JWT in localStorage (XSS risk)
❌ No rate limiting on login
❌ No input validation
❌ Using raw SQL queries
❌ No HTTPS
❌ No helmet
❌ Exposing stack traces

---

# 🧪 Exercises (Important)

---

### Exercise 1

Add:

* Helmet
* Rate limiting
* Disable x-powered-by

To your existing project.

---

### Exercise 2

Implement:

```plaintext id="56l2i2"
POST /login
```

Rate limit to 5 attempts per 10 minutes.

---

### Exercise 3

Secure file upload:

* Only images
* Max 2MB
* Validate MIME type

---

# 🎯 After Module 12 You Must Master

* Attack categories
* Helmet usage
* CORS configuration
* CSRF vs XSS difference
* Rate limiting
* SQL injection prevention
* Environment variable security
* Secure cookie configuration
* Production security mindset

---


