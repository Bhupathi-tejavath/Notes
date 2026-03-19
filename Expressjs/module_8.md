

---

# 📘 MODULE 8: REST API Development (Professional API Architecture)

This module covers:

1. What is REST?
2. REST architectural constraints
3. Resource-oriented design
4. Designing RESTful routes properly
5. CRUD mapping
6. Status code correctness
7. Structuring API responses
8. API versioning
9. Pagination
10. Filtering
11. Sorting
12. Searching
13. Partial responses
14. Idempotency
15. Error response structure
16. HATEOAS (intro)
17. API best practices
18. Common mistakes
19. Exercises

---

# 1️⃣ What is REST?

---

## Definition

REST (Representational State Transfer) is an architectural style for designing networked applications.

It is:

* Stateless
* Resource-based
* Uses HTTP methods semantically
* Transfers representations (usually JSON)

---

## REST is NOT:

* A protocol
* A library
* A framework

It is a **design philosophy**.

---

# 2️⃣ REST Architectural Constraints

To be RESTful, an API must follow:

---

## 1. Client-Server Separation

Frontend and backend are independent.

---

## 2. Stateless

Each request must contain all necessary information.

Server does NOT store session state between requests.

---

## 3. Cacheable

Responses should indicate if cacheable.

---

## 4. Uniform Interface

Consistent URL structure and HTTP usage.

---

## 5. Layered System

Client doesn’t know if talking to DB, load balancer, or service.

---

# 3️⃣ Resource-Oriented Design

In REST:

> Everything is a resource.

Examples:

* users
* products
* orders
* posts

Resources are nouns — NOT verbs.

---

## ❌ Bad

```plaintext
/getUsers
/createUser
/deleteUser
```

---

## ✔ Good

```plaintext
/users
/users/:id
```

HTTP method defines action.

---

# 4️⃣ Designing RESTful Routes Properly

---

## Standard CRUD Mapping

| Operation | HTTP   | Route      |
| --------- | ------ | ---------- |
| Get all   | GET    | /users     |
| Get one   | GET    | /users/:id |
| Create    | POST   | /users     |
| Replace   | PUT    | /users/:id |
| Update    | PATCH  | /users/:id |
| Delete    | DELETE | /users/:id |

---

## Example Implementation

```js
router.get('/users', getUsers);
router.get('/users/:id', getUserById);
router.post('/users', createUser);
router.put('/users/:id', replaceUser);
router.patch('/users/:id', updateUser);
router.delete('/users/:id', deleteUser);
```

---

# 5️⃣ CRUD Implementation Example

---

## Controller Example

```js
async function getUsers(req, res) {
    const users = await User.find();
    res.status(200).json({
        success: true,
        data: users
    });
}
```

---

## Create

```js
async function createUser(req, res) {
    const user = await User.create(req.body);

    res.status(201).json({
        success: true,
        data: user
    });
}
```

---

# 6️⃣ Status Code Correctness (Professional Usage)

---

## 200 OK → successful retrieval

## 201 Created → resource created

## 204 No Content → successful delete

## 400 Bad Request → invalid input

## 401 Unauthorized → not logged in

## 403 Forbidden → no permission

## 404 Not Found → resource absent

## 409 Conflict → duplicate

## 500 Internal Server Error → server issue

---

## Example: Delete

```js
res.status(204).send();
```

No body for 204.

---

# 7️⃣ Structuring API Responses (Professional Standard)

Avoid:

```json
{
  "name": "Pradeep"
}
```

Prefer consistent format:

```json
{
  "success": true,
  "data": {
    "name": "Pradeep"
  }
}
```

---

## Error Format

```json
{
  "success": false,
  "error": {
    "message": "User not found",
    "code": 404
  }
}
```

Consistency is critical.

---

# 8️⃣ API Versioning

---

## URL Versioning (Most Common)

```plaintext
/api/v1/users
```

Implementation:

```js
app.use('/api/v1', v1Routes);
```

---

## Why Version?

* Avoid breaking old clients
* Enable smooth upgrades

---

# 9️⃣ Pagination

---

## Why Pagination?

Avoid sending 10,000 records at once.

---

## Query-Based Pagination

```plaintext
GET /users?page=2&limit=10
```

---

## Implementation

```js
const page = Number(req.query.page) || 1;
const limit = Number(req.query.limit) || 10;

const skip = (page - 1) * limit;

const users = await User.find()
    .skip(skip)
    .limit(limit);
```

---

## Response Format

```json
{
  "success": true,
  "page": 2,
  "limit": 10,
  "total": 100,
  "data": [...]
}
```

---

# 🔟 Filtering

---

## Example

```plaintext
GET /users?role=admin
```

---

## Implementation

```js
const filter = {};

if (req.query.role) {
    filter.role = req.query.role;
}

const users = await User.find(filter);
```

---

# 1️⃣1️⃣ Sorting

---

## Example

```plaintext
GET /users?sort=createdAt
GET /users?sort=-createdAt
```

---

## Implementation

```js
const sort = req.query.sort || 'createdAt';

const users = await User.find().sort(sort);
```

`-` means descending.

---

# 1️⃣2️⃣ Searching

---

## Example

```plaintext
GET /users?search=Pra
```

---

## Implementation (MongoDB)

```js
const search = req.query.search;

const users = await User.find({
    name: { $regex: search, $options: 'i' }
});
```

---

# 1️⃣3️⃣ Partial Responses (Field Selection)

---

## Example

```plaintext
GET /users?fields=name,email
```

---

## Implementation

```js
const fields = req.query.fields?.split(',').join(' ');

const users = await User.find().select(fields);
```

---

# 1️⃣4️⃣ Idempotency

---

## Definition

An operation is idempotent if multiple identical requests produce the same result.

| Method | Idempotent? |
| ------ | ----------- |
| GET    | Yes         |
| PUT    | Yes         |
| DELETE | Yes         |
| POST   | No          |
| PATCH  | Usually No  |

---

Example:

Calling DELETE multiple times should not break system.

---

# 1️⃣5️⃣ HATEOAS (Intro)

Hypermedia As The Engine Of Application State.

Response includes navigation links.

Example:

```json
{
  "data": {...},
  "links": {
    "self": "/users/1",
    "posts": "/users/1/posts"
  }
}
```

Not always required but enterprise-level concept.

---

# 1️⃣6️⃣ API Best Practices

---

✔ Use nouns, not verbs
✔ Use plural resources
✔ Use proper status codes
✔ Version your API
✔ Validate input
✔ Consistent response format
✔ Secure endpoints
✔ Document your API

---

# 1️⃣7️⃣ Common Mistakes

---

❌ Mixing REST with RPC
❌ Using GET for deletion
❌ Returning 200 for everything
❌ No pagination
❌ Inconsistent response structure
❌ No validation

---

# 🧪 Exercises

---

### Exercise 1

Build:

```plaintext
/api/v1/products
```

Support:

* Pagination
* Filtering by category
* Sorting by price

---

### Exercise 2

Create:

```plaintext
DELETE /users/:id
```

Return 204 if successful.

---

### Exercise 3

Implement field selection:

```plaintext
GET /users?fields=name,email
```

---

# 🎯 After Module 8 You Must Master

* REST architecture principles
* Proper CRUD route design
* Professional API response structure
* Pagination, filtering, sorting
* Idempotency
* Versioning
* Production API thinking

---


