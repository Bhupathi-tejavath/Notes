
---

# 📘 MODULE 6: Working with Forms & Data (Deep Handling & Validation)

---

## 1️⃣ Understanding HTTP Request Body Types

When a client sends data to the server, it is usually sent in one of these formats:

| Content-Type                        | Used For               |
| ----------------------------------- | ---------------------- |
| `application/json`                  | JSON APIs              |
| `application/x-www-form-urlencoded` | Traditional HTML forms |
| `multipart/form-data`               | File uploads           |
| `text/plain`                        | Raw text               |

Server must parse body before using it.

---

# 2️⃣ Parsing JSON Payload

---

## What is JSON Payload?

Client sends:

```http
POST /users
Content-Type: application/json
```

```json
{
  "name": "Pradeep",
  "age": 22
}
```

---

## Enabling JSON Parsing

```js
app.use(express.json());
```

This middleware:

* Reads request stream
* Parses JSON
* Populates `req.body`

---

## Example

```js
app.post('/users', (req, res) => {
    console.log(req.body);
    res.json({
        received: req.body
    });
});
```

---

## What Happens Internally?

1. Express reads raw body stream
2. Buffers data
3. Parses JSON
4. Attaches parsed object to `req.body`

---

## Common Error

If JSON is invalid:

```json
{ name: Pradeep }
```

Server throws:

```plaintext
SyntaxError: Unexpected token
```

Handled via error middleware.

---

# 3️⃣ Parsing URL-Encoded Data

---

## What is URL-Encoded?

Used in traditional HTML forms.

Example:

```html
<form method="POST">
  <input name="username">
</form>
```

Sends:

```plaintext
username=Pradeep&age=22
```

---

## Middleware

```js
app.use(express.urlencoded({ extended: true }));
```

---

## What is `extended`?

* `false` → simple key-value pairs
* `true` → supports nested objects using `qs` library

Example nested:

```plaintext
user[name]=Pradeep&user[age]=22
```

Produces:

```js
{
  user: {
    name: 'Pradeep',
    age: '22'
  }
}
```

---

# 4️⃣ Handling Multipart Form Data (File Uploads)

---

`multipart/form-data` is used when uploading files.

Express alone cannot parse this.

We use **Multer**.

---

## Installing Multer

```bash
npm install multer
```

---

## Basic Setup

```js
const multer = require('multer');

const upload = multer({
    dest: 'uploads/'
});
```

---

## Single File Upload

```js
app.post('/upload', upload.single('file'), (req, res) => {
    console.log(req.file);
    res.send('File uploaded');
});
```

---

## What is `req.file`?

Contains:

```js
{
  fieldname: 'file',
  originalname: 'image.png',
  encoding: '7bit',
  mimetype: 'image/png',
  destination: 'uploads/',
  filename: 'abc123',
  path: 'uploads/abc123',
  size: 10245
}
```

---

## Multiple Files

```js
upload.array('photos', 5)
```

Access via:

```js
req.files
```

---

# 5️⃣ Custom File Storage Configuration

---

## Disk Storage Engine

```js
const storage = multer.diskStorage({
    destination: (req, file, cb) => {
        cb(null, 'uploads/');
    },
    filename: (req, file, cb) => {
        cb(null, Date.now() + '-' + file.originalname);
    }
});

const upload = multer({ storage });
```

---

## File Filter (Security)

```js
const upload = multer({
    storage,
    fileFilter: (req, file, cb) => {
        if (file.mimetype === 'image/png') {
            cb(null, true);
        } else {
            cb(new Error('Only PNG allowed'));
        }
    }
});
```

---

# 6️⃣ Input Validation (Critical for Security)

Never trust `req.body`.

---

## Manual Validation Example

```js
app.post('/register', (req, res) => {
    const { email, password } = req.body;

    if (!email || !password) {
        return res.status(400).json({
            message: 'Missing fields'
        });
    }

    if (password.length < 6) {
        return res.status(400).json({
            message: 'Password too short'
        });
    }

    res.status(201).json({ message: 'Registered' });
});
```

---

# 7️⃣ Using express-validator (Professional Approach)

---

## Install

```bash
npm install express-validator
```

---

## Example

```js
const { body, validationResult } = require('express-validator');

app.post('/register',
    body('email').isEmail(),
    body('password').isLength({ min: 6 }),
    (req, res) => {
        const errors = validationResult(req);

        if (!errors.isEmpty()) {
            return res.status(400).json({
                errors: errors.array()
            });
        }

        res.json({ message: 'Valid data' });
    }
);
```

---

# 8️⃣ Data Sanitization

Sanitization prevents:

* XSS
* Injection attacks
* Malformed input

Example:

```js
body('email').trim().escape()
```

---

## Trim

Removes whitespace.

## Escape

Converts:

```plaintext
<script>
```

To safe HTML.

---

# 9️⃣ Handling Large Payloads

---

Default limit:

```plaintext
100kb
```

Increase limit:

```js
app.use(express.json({ limit: '10mb' }));
```

---

# 🔟 Security Best Practices for Data Handling

---

### ✔ Always validate input

### ✔ Always sanitize input

### ✔ Restrict file types

### ✔ Restrict file size

### ✔ Use environment variables

### ✔ Do not log sensitive data

### ✔ Never trust client

---

# 1️⃣1️⃣ Handling Errors in Body Parsing

---

Invalid JSON triggers error middleware.

Add:

```js
app.use((err, req, res, next) => {
    if (err.type === 'entity.parse.failed') {
        return res.status(400).json({
            message: 'Invalid JSON'
        });
    }
    next(err);
});
```

---

# 1️⃣2️⃣ Production Pattern Example

Full example:

```js
app.use(express.json({ limit: '5mb' }));
app.use(express.urlencoded({ extended: true }));

app.post('/api/v1/users',
    body('email').isEmail(),
    body('password').isLength({ min: 8 }),
    asyncHandler(async (req, res) => {

        const errors = validationResult(req);
        if (!errors.isEmpty()) {
            return res.status(400).json({ errors: errors.array() });
        }

        const user = await createUser(req.body);

        res.status(201).json({
            success: true,
            data: user
        });
    })
);
```

---

# 🧪 Exercises

---

### Exercise 1

Create:

```plaintext
POST /contact
```

* Accept name, email, message
* Validate email format
* Return 400 if invalid

---

### Exercise 2

Create file upload endpoint:

```plaintext
POST /profile-picture
```

* Only accept JPEG
* Max size 2MB

---

### Exercise 3

Create:

```plaintext
POST /product
```

Validate:

* name required
* price must be numeric
* quantity must be integer

---

# 🎯 After Module 6 You Must Master

* How request body parsing works internally
* Difference between JSON, URL encoded, multipart
* How Multer handles file uploads
* Validation vs sanitization
* Secure file handling
* Production-level input validation

---

