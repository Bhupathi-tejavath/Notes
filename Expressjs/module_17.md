
---

# 📘 MODULE 17: Deployment & DevOps (Production Deployment Mastery)

This module covers:

1. What “production-ready” really means
2. Preparing Express app for production
3. Environment configuration
4. Using PM2 in production
5. Reverse proxy with Nginx
6. HTTPS setup (SSL)
7. Deployment platforms (Render, Railway, AWS, VPS)
8. CI/CD basics
9. Environment variables in production
10. Logging in production
11. Monitoring & health checks
12. Zero-downtime deployment
13. Production checklist
14. Common deployment mistakes
15. Exercises

---

# 1️⃣ What Does Production-Ready Mean?

Production-ready app must:

✔ Handle errors properly
✔ Use environment variables
✔ Be secure (Helmet, rate limit, etc.)
✔ Use logging
✔ Use process manager
✔ Restart automatically on crash
✔ Use HTTPS
✔ Be scalable

---

# 2️⃣ Preparing Express App for Production

---

## Use Environment Variables

Never hardcode:

```js
const PORT = 3000;
const DB_PASSWORD = "123456";
```

Instead:

```js
const PORT = process.env.PORT || 3000;
```

---

## Add Production Script

```json
"scripts": {
  "start": "node server.js",
  "dev": "nodemon server.js"
}
```

---

## Separate app.js and server.js

Good structure:

```plaintext
app.js      → Express config
server.js   → Start server
```

---

### app.js

```js
const express = require('express');
const app = express();

// middleware
// routes

module.exports = app;
```

---

### server.js

```js
const app = require('./app');

const PORT = process.env.PORT || 3000;

app.listen(PORT, () => {
    console.log(`Server running on ${PORT}`);
});
```

This improves testability.

---

# 3️⃣ Environment Configuration

---

## Use dotenv (for development)

```bash
npm install dotenv
```

```js
require('dotenv').config();
```

---

## .env file

```plaintext
PORT=3000
JWT_SECRET=mysecret
DB_HOST=localhost
```

---

## In Production

Environment variables are set directly on server or hosting platform.

Never upload `.env` file to GitHub.

---

# 4️⃣ Using PM2 in Production

PM2 is a production process manager.

---

## Install

```bash
npm install -g pm2
```

---

## Start App

```bash
pm2 start server.js
```

---

## Cluster Mode (Recommended)

```bash
pm2 start server.js -i max
```

Uses all CPU cores.

---

## Useful Commands

```bash
pm2 list
pm2 logs
pm2 restart server
pm2 stop server
```

---

## Auto Start on Reboot

```bash
pm2 startup
pm2 save
```

---

# 5️⃣ Reverse Proxy with Nginx

Why use Nginx?

✔ Handles static files
✔ Handles SSL
✔ Load balancing
✔ Security

---

## Architecture

```plaintext
Client
   ↓
Nginx (Port 80/443)
   ↓
Express (Port 3000)
```

---

## Basic Nginx Config

```nginx
server {
    listen 80;
    server_name yourdomain.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
    }
}
```

---

# 6️⃣ HTTPS Setup (SSL)

Never deploy production app without HTTPS.

---

## Use Let's Encrypt (Free SSL)

Install certbot:

```bash
sudo certbot --nginx
```

Automatically configures SSL.

---

## After SSL

Users access:

```plaintext
https://yourdomain.com
```

Secure encrypted connection.

---

# 7️⃣ Deployment Platforms

---

## 1. Render

Easy deployment:

Steps:

1. Push code to GitHub
2. Connect repo to Render
3. Add environment variables
4. Deploy

---

## 2. Railway

Similar to Render.

---

## 3. VPS (DigitalOcean / AWS EC2)

Manual deployment:

```bash
ssh into server
git clone repo
npm install
pm2 start server.js
```

More control, more complexity.

---

## 4. AWS (Advanced)

Use:

* EC2
* Elastic Beanstalk
* Load balancer
* RDS

Enterprise-level deployment.

---

# 8️⃣ CI/CD Basics

CI/CD = Continuous Integration / Continuous Deployment.

---

## CI Process

```plaintext
Push Code
   ↓
Run Tests
   ↓
Build
   ↓
Deploy
```

---

## GitHub Actions Example

```yaml
name: Deploy

on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - run: npm install
      - run: npm test
```

Tests must pass before deployment.

---

# 9️⃣ Logging in Production

Use Winston or external logging.

---

## Store Logs

```plaintext
logs/
  error.log
  combined.log
```

---

## Monitor Logs

```bash
pm2 logs
```

Or use:

* Datadog
* New Relic
* Loggly

---

# 🔟 Monitoring & Health Checks

---

## Add Health Route

```js
app.get('/health', (req, res) => {
    res.status(200).json({ status: 'OK' });
});
```

Load balancer checks this endpoint.

---

## Monitor Metrics

* CPU usage
* Memory usage
* Request rate
* Error rate

---

# 1️⃣1️⃣ Zero-Downtime Deployment

Using PM2 reload:

```bash
pm2 reload server
```

Or cluster mode.

Prevents downtime during deployment.

---

# 1️⃣2️⃣ Production Deployment Checklist

---

✔ Use HTTPS
✔ Use PM2 cluster mode
✔ Use Nginx reverse proxy
✔ Use environment variables
✔ Use rate limiting
✔ Enable compression
✔ Add logging
✔ Add health check
✔ Use database connection pooling
✔ Disable x-powered-by
✔ Monitor memory usage
✔ Enable automatic restart

---

# 1️⃣3️⃣ Common Deployment Mistakes

---

❌ Hardcoding secrets
❌ No HTTPS
❌ Running with `node server.js` only
❌ No process manager
❌ No environment variables
❌ No error handling
❌ Using development DB in production
❌ No backup strategy

---

# 🧪 Exercises (Important for You)

---

### Exercise 1

Deploy your student portal to Render.

Add:

* Environment variables
* JWT secret
* MySQL connection

---

### Exercise 2

Install PM2 and run:

```bash
pm2 start server.js -i max
```

Check CPU usage.

---

### Exercise 3

Add `/health` endpoint.

Test using browser.

---

# 🎯 After Module 17 You Must Master

* Production readiness principles
* Environment variable usage
* PM2 cluster mode
* Nginx reverse proxy
* HTTPS setup
* CI/CD basics
* Logging & monitoring
* Zero-downtime deployment

---

