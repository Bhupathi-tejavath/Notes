

---

# 📘 MODULE 14: Advanced Architecture Patterns (Enterprise-Level Design)

This module covers:

1. Why architecture matters
2. Layered architecture overview
3. MVC pattern in Express
4. Service layer architecture
5. Repository pattern
6. Clean Architecture principles
7. Dependency Injection (DI)
8. Modular project structure
9. Multi-role system design (relevant to you)
10. Microservices basics
11. API Gateway concept
12. Monolith vs Microservices
13. Event-driven architecture
14. Scalable folder structure example
15. Production architectural best practices
16. Common mistakes
17. Exercises

---

# 1️⃣ Why Architecture Matters

Without architecture:

* Routes contain business logic
* Business logic contains DB queries
* No separation of concerns
* Hard to test
* Hard to scale
* Hard to debug

With architecture:

* Clear responsibilities
* Reusable services
* Testable components
* Scalable system

---

# 2️⃣ Layered Architecture Overview

Basic backend layers:

```plaintext
Client
   ↓
Routes
   ↓
Controllers
   ↓
Services
   ↓
Repositories / Models
   ↓
Database
```

Each layer has a clear responsibility.

---

# 3️⃣ MVC Pattern in Express

MVC = Model View Controller

---

## Model

Represents database structure.

Example:

```js
// user.model.js
```

Handles data schema and DB interaction.

---

## View

Template layer (EJS / frontend).

---

## Controller

Handles request and response.

Example:

```js
async function getUsers(req, res) {
    const users = await User.find();
    res.json(users);
}
```

---

## Route

Connects URL to controller.

```js
router.get('/users', getUsers);
```

---

## MVC Folder Structure

```plaintext
src/
├── models/
├── views/
├── controllers/
├── routes/
```

---

# 4️⃣ Service Layer Architecture

MVC alone becomes messy in large systems.

Service layer extracts business logic.

---

## Why Service Layer?

Controller should NOT contain heavy logic.

Bad:

```js
async function createUser(req, res) {
    const user = await User.create(req.body);
    sendEmail(user.email);
    logActivity(user.id);
    res.json(user);
}
```

---

Better:

```plaintext
Controller → calls Service → Service handles logic
```

---

## Service Example

```js
// user.service.js
async function createUser(data) {
    const user = await User.create(data);
    await sendWelcomeEmail(user.email);
    return user;
}
```

---

Controller:

```js
async function createUserController(req, res) {
    const user = await userService.createUser(req.body);
    res.status(201).json(user);
}
```

---

Benefits:

✔ Clean controllers
✔ Reusable business logic
✔ Easier testing

---

# 5️⃣ Repository Pattern

Used to abstract database access.

---

Instead of calling:

```js
User.find()
```

Directly in service, use repository.

---

## Repository Example

```js
// user.repository.js
async function findAll() {
    return User.find();
}

async function findById(id) {
    return User.findById(id);
}
```

---

Service:

```js
const userRepo = require('./user.repository');

async function getUsers() {
    return userRepo.findAll();
}
```

---

Benefits:

✔ DB logic isolated
✔ Easy DB replacement
✔ Cleaner testing

---

# 6️⃣ Clean Architecture Principles

Clean Architecture separates:

* Business rules
* Application logic
* Frameworks
* Database

Core idea:

> Business logic should not depend on Express or database.

---

Layered Clean Architecture:

```plaintext
Routes
  ↓
Controllers
  ↓
Use Cases (Business Logic)
  ↓
Repositories (Interfaces)
  ↓
Database
```

---

Advantages:

✔ Highly testable
✔ Replace Express easily
✔ Replace DB easily
✔ Enterprise-level maintainability

---

# 7️⃣ Dependency Injection (DI)

Instead of importing directly:

```js
const userRepo = require('./repo');
```

Inject dependency:

```js
function createUserService(userRepo) {
    return {
        async create(data) {
            return userRepo.save(data);
        }
    };
}
```

Improves:

✔ Testing
✔ Flexibility
✔ Decoupling

---

# 8️⃣ Modular Project Structure (Recommended for You)

For your Smart Student Hub:

```plaintext
src/
├── modules/
│   ├── student/
│   │   ├── student.controller.js
│   │   ├── student.service.js
│   │   ├── student.repository.js
│   │   ├── student.routes.js
│   │
│   ├── faculty/
│   ├── admin/
│   ├── auth/
│
├── middleware/
├── utils/
├── config/
├── app.js
```

Each module self-contained.

---

# 9️⃣ Multi-Role System Design (Your Use Case)

You have:

* student
* faculty
* admin

Better design:

---

## Option 1: Single User Table + Role Column

```plaintext
users
 id
 name
 email
 password
 role
```

Role-based middleware controls access.

---

## Option 2: Separate Tables (Your current approach)

```plaintext
students
faculty
admins
```

Pros:

* Clear separation
  Cons:
* More complexity

Architecturally, single table + role is cleaner.

---

# 🔟 Monolith vs Microservices

---

## Monolith

Single application:

```plaintext
Student + Faculty + Admin + Auth
```

Pros:

✔ Simple
✔ Easy to deploy

Cons:

❌ Hard to scale independently

---

## Microservices

Separate services:

```plaintext
Auth Service
Student Service
Faculty Service
Notification Service
```

Communicate via:

* HTTP
* Message queues

---

# 1️⃣1️⃣ API Gateway Concept

In microservices:

```plaintext
Client
   ↓
API Gateway
   ↓
Service A
Service B
```

Gateway handles:

* Authentication
* Rate limiting
* Routing

---

# 1️⃣2️⃣ Event-Driven Architecture

Instead of:

```plaintext
User created → directly send email
```

Use event:

```plaintext
UserCreated event
```

Listener:

```plaintext
Email service listens → sends email
```

Tools:

* RabbitMQ
* Kafka

Improves scalability.

---

# 1️⃣3️⃣ Scalable Folder Structure Example

```plaintext
src/
├── config/
│   ├── database.js
│   ├── env.js
│
├── core/
│   ├── error.js
│   ├── asyncHandler.js
│
├── modules/
│   ├── auth/
│   ├── users/
│   ├── activities/
│
├── app.js
├── server.js
```

---

# 1️⃣4️⃣ Production Architectural Best Practices

---

✔ Separate concerns strictly
✔ Use service layer
✔ Use repository abstraction
✔ Use centralized error handling
✔ Keep controllers thin
✔ Use dependency injection
✔ Use modular structure
✔ Plan for scaling
✔ Avoid circular dependencies

---

# 1️⃣5️⃣ Common Mistakes

---

❌ Putting all logic in routes
❌ Massive controller files
❌ No service layer
❌ DB queries inside route
❌ No module separation
❌ Circular imports
❌ No architecture planning

---

# 🧪 Exercises (Very Important for You)

---

### Exercise 1

Refactor one of your modules into:

* Controller
* Service
* Repository

---

### Exercise 2

Design clean structure for:

```plaintext
Activity Submission + Faculty Approval
```

Using service layer.

---

### Exercise 3

Create role-based architecture using:

```plaintext
Single user table + role column
```

Refactor middleware accordingly.

---

# 🎯 After Module 14 You Must Master

* MVC in Express
* Service layer architecture
* Repository pattern
* Clean architecture principles
* Dependency injection
* Modular project design
* Monolith vs microservices
* API Gateway concept
* Enterprise backend thinking

---

