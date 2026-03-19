
---

# 📘 MODULE 18: Advanced Topics (Enterprise & Modern Backend Patterns)

This module covers:

1. Streams in Express
2. File streaming & large data handling
3. Background jobs
4. Message queues (RabbitMQ)
5. Redis advanced usage
6. GraphQL with Express
7. TypeScript with Express
8. Serverless Express
9. API documentation (Swagger/OpenAPI)
10. Advanced validation architecture
11. Multi-tenant architecture
12. Advanced caching strategies
13. Advanced rate limiting strategies
14. Webhooks
15. Observability & tracing
16. Production-grade folder blueprint
17. Exercises

---

# 1️⃣ Streams in Express

---

## What is a Stream?

A stream processes data **chunk by chunk**, instead of loading everything into memory.

Useful for:

* Large file downloads
* Video streaming
* Large CSV exports
* Big JSON responses

---

## Problem Without Streams

```js
const data = fs.readFileSync('hugeFile.txt');
res.send(data);
```

Loads entire file into memory.

Bad for performance.

---

## Using Streams

```js
const fs = require('fs');

app.get('/download', (req, res) => {
    const stream = fs.createReadStream('hugeFile.txt');

    stream.pipe(res);
});
```

Benefits:

✔ Low memory usage
✔ Efficient
✔ Scalable

---

# 2️⃣ File Streaming Example (CSV Export)

---

```js
app.get('/export', async (req, res) => {
    res.setHeader('Content-Type', 'text/csv');
    res.setHeader('Content-Disposition', 'attachment; filename=data.csv');

    const stream = fs.createReadStream('data.csv');
    stream.pipe(res);
});
```

Used in:

* Reports
* Data exports (like NAAC reports in your project)

---

# 3️⃣ Background Jobs

Some tasks should not block request-response cycle.

Examples:

* Sending email
* Generating PDF
* Generating AI portfolio
* Heavy processing

---

## Problem

```js
app.post('/register', async (req, res) => {
    await sendWelcomeEmail(); // slow
    res.send('User created');
});
```

User waits.

---

## Solution: Background Jobs

---

## Using Bull (Redis-based Queue)

Install:

```bash
npm install bull
```

---

## Setup Queue

```js
const Queue = require('bull');

const emailQueue = new Queue('emailQueue', {
    redis: { host: '127.0.0.1', port: 6379 }
});
```

---

## Add Job

```js
emailQueue.add({
    email: 'user@test.com'
});
```

---

## Process Job

```js
emailQueue.process(async (job) => {
    await sendEmail(job.data.email);
});
```

Now request responds immediately.

---

# 4️⃣ Message Queues (RabbitMQ)

For microservices communication.

---

## Why Message Queue?

Instead of:

```plaintext
Service A → directly calls → Service B
```

Use:

```plaintext
Service A → sends message → Queue → Service B processes
```

Decoupled architecture.

---

## Install amqplib

```bash
npm install amqplib
```

---

## Basic Producer

```js
const amqp = require('amqplib');

const connection = await amqp.connect('amqp://localhost');
const channel = await connection.createChannel();

await channel.assertQueue('taskQueue');
channel.sendToQueue('taskQueue', Buffer.from('Hello'));
```

---

## Consumer

```js
channel.consume('taskQueue', msg => {
    console.log(msg.content.toString());
});
```

---

# 5️⃣ Redis Advanced Usage

Beyond caching:

✔ Session storage
✔ Rate limiting storage
✔ Pub/Sub
✔ Distributed locking

---

## Pub/Sub Example

```js
client.subscribe('notifications');

client.on('message', (channel, message) => {
    console.log(message);
});
```

Used with real-time systems.

---

# 6️⃣ GraphQL with Express

GraphQL allows clients to request exactly the data they need.

---

## Install

```bash
npm install express-graphql graphql
```

---

## Basic Setup

```js
const { graphqlHTTP } = require('express-graphql');
const { buildSchema } = require('graphql');

const schema = buildSchema(`
    type Query {
        hello: String
    }
`);

const root = {
    hello: () => 'Hello world!'
};

app.use('/graphql', graphqlHTTP({
    schema,
    rootValue: root,
    graphiql: true
}));
```

---

## Difference REST vs GraphQL

| REST               | GraphQL           |
| ------------------ | ----------------- |
| Multiple endpoints | Single endpoint   |
| Over-fetching      | Exact fetching    |
| Fixed response     | Flexible response |

---

# 7️⃣ TypeScript with Express

TypeScript adds static typing.

---

## Install

```bash
npm install typescript ts-node @types/express
```

---

## Example Controller

```ts
import { Request, Response } from 'express';

export const getUsers = (req: Request, res: Response) => {
    res.json({ message: 'Typed!' });
};
```

Benefits:

✔ Type safety
✔ Better refactoring
✔ Enterprise maintainability

---

# 8️⃣ Serverless Express

Deploy Express without managing servers.

Platforms:

* AWS Lambda
* Vercel
* Netlify

---

## Using serverless-http

```bash
npm install serverless-http
```

---

```js
const serverless = require('serverless-http');

module.exports.handler = serverless(app);
```

---

## When to Use Serverless

✔ Low traffic apps
✔ Event-driven APIs
✔ Cost optimization

---

# 9️⃣ API Documentation (Swagger / OpenAPI)

Professional APIs must be documented.

---

## Install

```bash
npm install swagger-ui-express
```

---

## Setup

```js
const swaggerUi = require('swagger-ui-express');
const swaggerDocument = require('./swagger.json');

app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(swaggerDocument));
```

---

Accessible at:

```plaintext
http://localhost:3000/api-docs
```

---

# 🔟 Advanced Validation Architecture

Instead of validating in controller:

✔ Create validation layer
✔ Reusable schema

Example with Joi:

```bash
npm install joi
```

```js
const Joi = require('joi');

const schema = Joi.object({
    email: Joi.string().email().required()
});
```

---

# 1️⃣1️⃣ Multi-Tenant Architecture

Useful for SaaS.

---

## Example

Single database, multiple institutions:

```plaintext
collegeId column
```

Every query filtered by tenant:

```js
WHERE collegeId = req.user.collegeId
```

Used in university systems.

---

# 1️⃣2️⃣ Advanced Caching Strategy

✔ Cache only GET requests
✔ Cache expensive queries
✔ Use cache invalidation on update
✔ Use Redis TTL

Example:

```js
client.del('users');
```

After update.

---

# 1️⃣3️⃣ Advanced Rate Limiting Strategy

Instead of IP-based:

✔ User-based rate limit
✔ Role-based limit
✔ Login route stricter limits

---

# 1️⃣4️⃣ Webhooks

Webhooks allow external systems to notify your app.

Example:

Stripe sends payment confirmation to:

```plaintext
POST /webhook
```

---

## Example

```js
app.post('/webhook', (req, res) => {
    console.log(req.body);
    res.status(200).send();
});
```

Must verify signature.

---

# 1️⃣5️⃣ Observability & Tracing

Enterprise systems need:

✔ Logging
✔ Metrics
✔ Tracing

Tools:

* Prometheus
* Grafana
* OpenTelemetry

---

# 1️⃣6️⃣ Production-Grade Folder Blueprint (Final Architecture)

```plaintext
src/
├── config/
├── core/
│   ├── errors/
│   ├── logger/
│
├── modules/
│   ├── auth/
│   ├── users/
│   ├── activities/
│
├── queues/
├── websocket/
├── docs/
├── app.ts
├── server.ts
```

This is enterprise-level Express architecture.

---

# 🧪 Exercises (Advanced)

---

### Exercise 1

Implement:

```plaintext
GET /export-report
```

Stream CSV file.

---

### Exercise 2

Add background job to:

* Generate student PDF portfolio
* Process via queue

---

### Exercise 3

Add Swagger documentation to your student portal API.

---

### Exercise 4

Implement multi-tenant filtering using `collegeId`.

---

# 🎯 After Module 18 You Must Master

* Streams
* Background jobs
* Message queues
* Redis advanced patterns
* GraphQL
* TypeScript
* Serverless deployment
* Swagger documentation
* Multi-tenant architecture
* Enterprise backend blueprint

---

