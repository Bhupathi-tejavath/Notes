

---

# 📘 MODULE 13: Performance Optimization (Scaling & Speed Engineering)

This module covers:

1. Why performance optimization matters
2. Node.js performance fundamentals
3. Compression middleware
4. Caching strategies
5. HTTP caching headers
6. In-memory caching
7. Redis caching
8. Load balancing
9. Clustering in Node.js
10. PM2 process manager
11. Memory management basics
12. Avoiding blocking operations
13. Database optimization
14. Horizontal vs Vertical scaling
15. Production performance checklist
16. Common mistakes
17. Exercises

---

# 1️⃣ Why Performance Optimization Matters

If your backend:

* Responds slowly
* Crashes under load
* Consumes too much memory

Users experience:

* Slow dashboards
* Failed logins
* Timeouts

Performance = user experience + reliability.

---

# 2️⃣ Node.js Performance Fundamentals

Node.js uses:

* Single-threaded event loop
* Non-blocking I/O

Meaning:

✔ Excellent for I/O-heavy apps
❌ Bad for CPU-heavy blocking tasks

---

## Blocking Example (Bad)

```js
while (true) {}
```

This blocks entire server.

---

## Non-blocking Example

```js
setTimeout(() => {
    console.log("Async operation");
}, 1000);
```

Event loop remains free.

---

# 3️⃣ Compression Middleware

Reduces response size → faster network transfer.

---

## Install

```bash
npm install compression
```

---

## Setup

```js
const compression = require('compression');

app.use(compression());
```

---

## What It Does

* Compresses response using gzip
* Reduces JSON size
* Improves performance over network

---

# 4️⃣ Caching Strategies

Caching reduces repeated computation.

---

## Types of Caching

1. Client-side caching
2. Server-side caching
3. Database caching
4. CDN caching

---

# 5️⃣ HTTP Caching Headers

---

## Cache-Control Header

```js
res.set('Cache-Control', 'public, max-age=3600');
```

Meaning:

* Cache for 1 hour

---

## No Cache

```js
res.set('Cache-Control', 'no-store');
```

Use for sensitive data.

---

# 6️⃣ In-Memory Caching (Simple Example)

---

## Install

```bash
npm install node-cache
```

---

## Setup

```js
const NodeCache = require('node-cache');
const cache = new NodeCache({ stdTTL: 60 });
```

---

## Usage

```js
app.get('/users', async (req, res) => {
    const cached = cache.get('users');

    if (cached) {
        return res.json(cached);
    }

    const users = await User.find();
    cache.set('users', users);

    res.json(users);
});
```

---

## Limitation

* Lost on server restart
* Not scalable across multiple servers

---

# 7️⃣ Redis Caching (Production-Grade)

Redis is in-memory data store.

---

## Install

```bash
npm install redis
```

---

## Setup

```js
const redis = require('redis');
const client = redis.createClient();
```

---

## Cache Example

```js
const data = await client.get('users');

if (data) {
    return res.json(JSON.parse(data));
}

const users = await User.find();
await client.setEx('users', 60, JSON.stringify(users));

res.json(users);
```

Redis works across multiple server instances.

---

# 8️⃣ Load Balancing

---

## What is Load Balancing?

Distributes traffic across multiple servers.

---

Example:

```plaintext
Client
   ↓
Load Balancer
   ↓      ↓      ↓
Server1 Server2 Server3
```

---

Common tools:

* Nginx
* HAProxy
* Cloud load balancers

---

# 9️⃣ Clustering in Node.js

Node is single-threaded.
Clustering allows using multiple CPU cores.

---

## Basic Cluster Example

```js
const cluster = require('cluster');
const os = require('os');

if (cluster.isPrimary) {
    const numCPUs = os.cpus().length;

    for (let i = 0; i < numCPUs; i++) {
        cluster.fork();
    }
} else {
    app.listen(3000);
}
```

Now server runs on all cores.

---

# 🔟 PM2 (Production Process Manager)

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

## Cluster Mode

```bash
pm2 start server.js -i max
```

`max` = use all CPU cores.

---

## Benefits

✔ Auto-restart on crash
✔ Cluster support
✔ Monitoring
✔ Log management

---

# 1️⃣1️⃣ Memory Management Basics

---

## Common Memory Issues

* Memory leaks
* Large in-memory arrays
* Not closing DB connections

---

## Monitor Memory

```js
console.log(process.memoryUsage());
```

---

## Avoid

```js
let hugeArray = [];

app.get('/', () => {
    hugeArray.push(new Array(1000000));
});
```

This causes memory leak.

---

# 1️⃣2️⃣ Avoid Blocking Operations

---

## Bad

```js
const fs = require('fs');

const data = fs.readFileSync('largefile.txt');
```

Blocks thread.

---

## Good

```js
fs.readFile('largefile.txt', (err, data) => {});
```

Non-blocking.

---

# 1️⃣3️⃣ Database Optimization

---

✔ Use indexes
✔ Avoid SELECT *
✔ Use pagination
✔ Optimize queries
✔ Use connection pooling

---

Example MySQL Pool:

```js
const pool = mysql.createPool({
    connectionLimit: 10
});
```

---

# 1️⃣4️⃣ Horizontal vs Vertical Scaling

---

## Vertical Scaling

Increase CPU/RAM.

Simple but limited.

---

## Horizontal Scaling

Add more servers.

Requires:

* Load balancer
* Shared DB
* Redis for session

Preferred for large apps.

---

# 1️⃣5️⃣ Production Performance Checklist

---

✔ Enable compression
✔ Use caching
✔ Use connection pooling
✔ Use PM2 cluster mode
✔ Optimize DB queries
✔ Add indexes
✔ Avoid blocking code
✔ Use load balancer
✔ Monitor memory usage

---

# 1️⃣6️⃣ Common Mistakes

---

❌ No caching
❌ No compression
❌ Running single Node process in production
❌ Blocking synchronous code
❌ No DB indexes
❌ Returning massive payloads

---

# 🧪 Exercises

---

### Exercise 1

Add compression to your project.

Measure response size difference.

---

### Exercise 2

Implement Redis caching for:

```plaintext
GET /students
```

Cache for 60 seconds.

---

### Exercise 3

Run your app using:

```bash
pm2 start server.js -i max
```

Check CPU usage.

---

# 🎯 After Module 13 You Must Master

* Node performance fundamentals
* Compression middleware
* Caching strategies
* Redis usage
* Clustering
* PM2
* Load balancing
* Memory awareness
* Database optimization

---

