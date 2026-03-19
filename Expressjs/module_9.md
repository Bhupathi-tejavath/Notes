
---

# 📘 MODULE 9: Database Integration (MongoDB, Mongoose, MySQL, ORM Patterns)

This module covers:

1. Why database integration matters
2. Database types (SQL vs NoSQL)
3. Connecting MongoDB with Express
4. Using Mongoose (deep dive)
5. Schema design principles
6. CRUD with Mongoose
7. Middleware in Mongoose
8. Validation at DB layer
9. Connecting MySQL with Express
10. Using MySQL with `mysql2`
11. Sequelize ORM (SQL ORM)
12. Model–Controller separation
13. Transactions
14. Indexing basics
15. Production best practices
16. Common mistakes
17. Exercises

---

# 1️⃣ Why Database Integration Matters

Backend without database is temporary memory.

Database provides:

* Persistence
* Data integrity
* Concurrency control
* Query capabilities
* Relationships

In real systems (like your student portal), DB layer is the backbone.

---

# 2️⃣ SQL vs NoSQL

---

## SQL (Relational)

Examples:

* MySQL
* PostgreSQL

Characteristics:

* Tables
* Rows
* Columns
* Strict schema
* Relationships (foreign keys)
* ACID compliance

---

## NoSQL (Document-based)

Example:

* MongoDB

Characteristics:

* JSON-like documents
* Flexible schema
* Scales horizontally
* No joins (usually)

---

## When to Use What?

| Use Case                        | Recommended |
| ------------------------------- | ----------- |
| Complex relationships           | SQL         |
| Rapid development               | MongoDB     |
| Structured institutional system | SQL         |
| Analytics heavy                 | SQL         |

For your student hub → MySQL is correct choice.

---

# 3️⃣ Connecting MongoDB with Express

---

## Install

```bash
npm install mongoose
```

---

## Basic Connection

```js
const mongoose = require('mongoose');

mongoose.connect('mongodb://localhost:27017/mydb')
    .then(() => console.log('MongoDB connected'))
    .catch(err => console.log(err));
```

---

## Connection Options (Production)

```js
mongoose.connect(process.env.MONGO_URI, {
    useNewUrlParser: true,
    useUnifiedTopology: true
});
```

---

# 4️⃣ Mongoose Deep Dive

Mongoose is an ODM (Object Data Modeling) library.

It provides:

* Schema definition
* Validation
* Middleware
* Query builder

---

## Creating Schema

```js
const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
    name: {
        type: String,
        required: true
    },
    email: {
        type: String,
        unique: true
    },
    age: Number
}, { timestamps: true });
```

---

## Creating Model

```js
const User = mongoose.model('User', userSchema);
```

---

# 5️⃣ Schema Design Principles

---

✔ Use meaningful field names
✔ Avoid deep nesting unless necessary
✔ Use references for large relationships
✔ Add indexes on searchable fields
✔ Keep schema consistent

---

# 6️⃣ CRUD with Mongoose

---

## Create

```js
const user = await User.create({
    name: "Pradeep",
    email: "p@gmail.com"
});
```

---

## Read

```js
const users = await User.find();
const user = await User.findById(id);
```

---

## Update

```js
await User.findByIdAndUpdate(id, {
    name: "Updated"
}, { new: true });
```

---

## Delete

```js
await User.findByIdAndDelete(id);
```

---

# 7️⃣ Mongoose Middleware (Hooks)

---

## Pre-save Hook

```js
userSchema.pre('save', function(next) {
    console.log('Before saving');
    next();
});
```

Used for:

* Password hashing
* Validation
* Logging

---

# 8️⃣ Validation at DB Layer

---

## Required Field

```js
email: {
    type: String,
    required: true
}
```

---

## Custom Validator

```js
email: {
    type: String,
    validate: {
        validator: function(v) {
            return v.includes('@');
        },
        message: 'Invalid email'
    }
}
```

---

# 9️⃣ Connecting MySQL with Express

Since you’re already using MySQL, this is critical.

---

## Install mysql2

```bash
npm install mysql2
```

---

## Basic Connection

```js
const mysql = require('mysql2');

const connection = mysql.createConnection({
    host: 'localhost',
    user: 'root',
    password: 'password',
    database: 'college'
});

connection.connect();
```

---

## Query Example

```js
connection.query(
    'SELECT * FROM students',
    (err, results) => {
        console.log(results);
    }
);
```

---

# 🔟 Using Promise-Based MySQL

Better approach:

```js
const mysql = require('mysql2/promise');

const pool = mysql.createPool({
    host: 'localhost',
    user: 'root',
    database: 'college'
});
```

---

## Async Query

```js
const [rows] = await pool.query(
    'SELECT * FROM students WHERE id = ?',
    [id]
);
```

Using `?` prevents SQL injection.

---

# 1️⃣1️⃣ Sequelize ORM (SQL ORM)

---

## Install

```bash
npm install sequelize mysql2
```

---

## Setup

```js
const { Sequelize } = require('sequelize');

const sequelize = new Sequelize('college', 'root', 'password', {
    dialect: 'mysql'
});
```

---

## Define Model

```js
const Student = sequelize.define('Student', {
    name: {
        type: Sequelize.STRING,
        allowNull: false
    },
    email: Sequelize.STRING
});
```

---

## Sync

```js
await sequelize.sync();
```

---

## CRUD

```js
await Student.create({ name: 'Pradeep' });
const students = await Student.findAll();
```

---

# 1️⃣2️⃣ Model–Controller Separation

---

## Model File

```js
// student.model.js
module.exports = Student;
```

---

## Controller File

```js
async function getStudents(req, res) {
    const students = await Student.findAll();
    res.json(students);
}
```

---

## Route File

```js
router.get('/', getStudents);
```

Separation improves scalability.

---

# 1️⃣3️⃣ Transactions

---

## Why Transactions?

Ensure atomic operations.

Example:

* Deduct money
* Credit receiver

Both must succeed or rollback.

---

## MySQL Transaction

```js
const conn = await pool.getConnection();

try {
    await conn.beginTransaction();

    await conn.query('UPDATE accounts SET balance = balance - 100 WHERE id=1');
    await conn.query('UPDATE accounts SET balance = balance + 100 WHERE id=2');

    await conn.commit();
} catch (err) {
    await conn.rollback();
} finally {
    conn.release();
}
```

---

# 1️⃣4️⃣ Indexing Basics

Indexes improve search performance.

SQL:

```sql
CREATE INDEX idx_email ON students(email);
```

MongoDB:

```js
userSchema.index({ email: 1 });
```

---

# 1️⃣5️⃣ Production Best Practices

---

✔ Use connection pooling
✔ Use environment variables
✔ Never store passwords in plain text
✔ Use transactions for critical operations
✔ Add indexes on frequently queried fields
✔ Avoid raw SQL without parameter binding
✔ Separate DB logic from routes

---

# 1️⃣6️⃣ Common Mistakes

---

❌ Not using parameterized queries
❌ Mixing DB logic inside route
❌ Not handling DB errors
❌ No indexes
❌ Blocking queries

---

# 🧪 Exercises (Important for You)

---

### Exercise 1

In your student hub:

* Create `students` table
* Insert data via POST endpoint
* Fetch all students via GET

---

### Exercise 2

Create transaction:

* Insert student
* Insert academic record
* Rollback if second fails

---

### Exercise 3

Add index on:

```plaintext
email
```

And measure performance.

---

# 🎯 After Module 9 You Must Master

* SQL vs NoSQL differences
* MongoDB + Mongoose integration
* MySQL integration using mysql2
* Sequelize ORM
* Transactions
* Validation at DB layer
* Clean architecture with models/controllers
* Secure query practices

---

