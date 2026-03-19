

---

# 📘 MODULE 16: Real-Time Applications (WebSockets & Socket.io)

This module covers:

1. Why real-time systems are different
2. HTTP vs WebSocket
3. WebSocket protocol fundamentals
4. Implementing WebSocket in Node
5. What is Socket.io?
6. Integrating Socket.io with Express
7. Events in Socket.io
8. Broadcasting
9. Rooms
10. Namespaces
11. Authentication in Socket.io
12. Scaling real-time apps
13. Redis adapter for scaling
14. Common mistakes
15. Exercises

---

# 1️⃣ Why Real-Time Systems Are Different

Normal HTTP:

```plaintext
Client → Request → Server → Response → Connection Closed
```

Real-time systems:

```plaintext
Client ↔ Server (persistent connection)
```

Connection stays open.

Server can push data without client requesting again.

---

# 2️⃣ HTTP vs WebSocket

| Feature    | HTTP            | WebSocket           |
| ---------- | --------------- | ------------------- |
| Connection | Short-lived     | Persistent          |
| Direction  | Client → Server | Bi-directional      |
| Overhead   | Higher          | Low after handshake |
| Use Case   | REST APIs       | Chat, notifications |

---

# 3️⃣ WebSocket Protocol Fundamentals

WebSocket starts as HTTP handshake:

```plaintext
GET /chat HTTP/1.1
Upgrade: websocket
Connection: Upgrade
```

Server responds:

```plaintext
HTTP/1.1 101 Switching Protocols
```

After that:

* TCP connection remains open
* Data flows both ways

---

# 4️⃣ Implementing Raw WebSocket in Node

---

## Install ws

```bash
npm install ws
```

---

## Basic Example

```js
const WebSocket = require('ws');

const wss = new WebSocket.Server({ port: 8080 });

wss.on('connection', ws => {
    console.log('Client connected');

    ws.on('message', message => {
        console.log('Received:', message);
        ws.send('Message received');
    });
});
```

Raw WebSocket works but lacks features like:

* Rooms
* Auto-reconnect
* Fallback transport

That’s why we use Socket.io.

---

# 5️⃣ What is Socket.io?

Socket.io is a real-time engine built on top of WebSocket.

It provides:

✔ Automatic fallback (polling if WebSocket fails)
✔ Event-based communication
✔ Rooms
✔ Namespaces
✔ Built-in reconnection

---

# 6️⃣ Integrating Socket.io with Express

---

## Install

```bash
npm install socket.io
```

---

## Setup Server

```js
const express = require('express');
const http = require('http');
const { Server } = require('socket.io');

const app = express();
const server = http.createServer(app);
const io = new Server(server);

server.listen(3000);
```

---

## Basic Connection

```js
io.on('connection', socket => {
    console.log('User connected:', socket.id);

    socket.on('disconnect', () => {
        console.log('User disconnected');
    });
});
```

---

# 7️⃣ Events in Socket.io

Socket.io is event-driven.

---

## Server Side

```js
io.on('connection', socket => {
    socket.on('message', data => {
        console.log(data);
    });
});
```

---

## Client Side

```js
const socket = io();

socket.emit('message', 'Hello Server');
```

---

## Sending Event from Server

```js
socket.emit('welcome', 'Welcome User');
```

---

# 8️⃣ Broadcasting

---

## Send to All Clients

```js
io.emit('announcement', 'Server message');
```

---

## Send to All Except Sender

```js
socket.broadcast.emit('newMessage', data);
```

---

# 9️⃣ Rooms

Rooms allow grouping users.

Example:

* Room: branch-CS
* Room: branch-IT

---

## Join Room

```js
socket.join('room1');
```

---

## Emit to Room

```js
io.to('room1').emit('message', 'Hello Room');
```

---

## Use Case (Your Voting System)

* Each branch joins its room
* Vote updates broadcast only to that branch

---

# 🔟 Namespaces

Namespaces divide connection paths.

Example:

```plaintext
/chat
/notifications
```

---

## Create Namespace

```js
const chatNamespace = io.of('/chat');

chatNamespace.on('connection', socket => {
    console.log('Chat connected');
});
```

Client connects:

```js
const socket = io('/chat');
```

---

# 1️⃣1️⃣ Authentication in Socket.io

Since WebSocket doesn't automatically use Express middleware, we verify token manually.

---

## Example

```js
io.use((socket, next) => {
    const token = socket.handshake.auth.token;

    if (!token) {
        return next(new Error('Unauthorized'));
    }

    next();
});
```

Client:

```js
const socket = io({
    auth: {
        token: 'your_jwt_here'
    }
});
```

---

# 1️⃣2️⃣ Scaling Real-Time Apps

Problem:

If using multiple servers:

```plaintext
Client A → Server 1
Client B → Server 2
```

If A sends message:

Server 1 doesn’t know about users connected to Server 2.

Solution:

Use Redis adapter.

---

# 1️⃣3️⃣ Redis Adapter for Scaling

---

## Install

```bash
npm install @socket.io/redis-adapter redis
```

---

## Setup

```js
const { createAdapter } = require('@socket.io/redis-adapter');
const { createClient } = require('redis');

const pubClient = createClient();
const subClient = pubClient.duplicate();

io.adapter(createAdapter(pubClient, subClient));
```

Now messages sync across servers.

---

# 1️⃣4️⃣ Common Mistakes

---

❌ Not handling disconnect events
❌ Not validating user input
❌ Not authenticating socket connections
❌ Storing large data in memory
❌ Forgetting to scale with Redis
❌ Blocking event loop

---

# 1️⃣5️⃣ Real-World Architecture Example

Chat system architecture:

```plaintext
Client
   ↓
Express Server
   ↓
Socket.io
   ↓
Redis (for scaling)
   ↓
Database (store messages)
```

---

# 🧪 Exercises (Highly Relevant to You)

---

### Exercise 1

Build simple chat:

* Users connect
* Send messages
* Broadcast to all

---

### Exercise 2

Create branch-based rooms:

```plaintext
branch-CS
branch-IT
```

Users join based on branch.

---

### Exercise 3

Secure socket connection using JWT.

---

# 🎯 After Module 16 You Must Master

* HTTP vs WebSocket difference
* WebSocket handshake
* Socket.io integration with Express
* Events and emit pattern
* Broadcasting and rooms
* Namespaces
* Socket authentication
* Scaling with Redis
* Real-time system architecture

---

