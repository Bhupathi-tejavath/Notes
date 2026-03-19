
---

# 📘 MODULE 5: Request & Response Objects (Internals Deep Dive)

We will cover:

1. What `req` and `res` really are
2. The `req` object – complete anatomy
3. The `res` object – complete anatomy
4. Headers handling
5. Cookies
6. IP & networking info
7. Content negotiation
8. Setting custom headers
9. Response lifecycle
10. Status codes in REST context
11. Advanced response patterns
12. Common mistakes
13. Exercises

---

# 1️⃣ What Are `req` and `res` Really?

---

When you create:

```js
const app = express();
```

Internally, Express wraps Node’s native:

```js
http.createServer((req, res) => {})
```

So:

* `req` is an enhanced version of `http.IncomingMessage`
* `res` is an enhanced version of `http.ServerResponse`

Express extends them with helper methods.

---

# 2️⃣ The Request Object (`req`) – Deep Anatomy

---

## Core Categories of Data in `req`

| Category  | Purpose                  |
| --------- | ------------------------ |
| URL Data  | params, query            |
| Body Data | req.body                 |
| Headers   | req.headers              |
| Cookies   | req.cookies              |
| Network   | req.ip                   |
| Metadata  | req.method, req.protocol |

---

## 2.1 `req.method`

HTTP method used.

```js
console.log(req.method);
```

Example output:

```
GET
POST
```

---

## 2.2 `req.url`

Original request URL.

```js
console.log(req.url);
```

Example:

```
/users/10?active=true
```

---

## 2.3 `req.params`

Dynamic route parameters.

Example:

```js
app.get('/users/:id', (req, res) => {
    console.log(req.params);
});
```

Request:

```
/users/45
```

Output:

```js
{ id: '45' }
```

⚠ Always string.

---

## 2.4 `req.query`

Query parameters.

Request:

```
/search?keyword=node&page=2
```

```js
console.log(req.query);
```

Output:

```js
{
  keyword: 'node',
  page: '2'
}
```

---

## 2.5 `req.body`

Contains parsed body data.

⚠ Only works if body-parsing middleware is enabled:

```js
app.use(express.json());
```

Example:

```js
app.post('/users', (req, res) => {
    console.log(req.body);
});
```

If client sends:

```json
{
  "name": "Pradeep"
}
```

Then:

```js
{ name: 'Pradeep' }
```

---

## 2.6 `req.headers`

All request headers.

```js
console.log(req.headers);
```

Example output:

```js
{
  host: 'localhost:3000',
  'content-type': 'application/json'
}
```

Headers are lowercase.

---

## 2.7 `req.get()`

Safer way to access specific header.

```js
req.get('Content-Type');
```

---

## 2.8 `req.cookies`

Requires cookie-parser middleware.

```bash
npm install cookie-parser
```

```js
const cookieParser = require('cookie-parser');
app.use(cookieParser());
```

Then:

```js
console.log(req.cookies);
```

---

## 2.9 `req.ip`

Client IP address.

```js
console.log(req.ip);
```

Behind proxy requires:

```js
app.set('trust proxy', true);
```

---

## 2.10 `req.protocol`

Returns:

```
http
https
```

---

# 3️⃣ The Response Object (`res`) – Deep Anatomy

---

## 3.1 `res.send()`

Universal sender.

Accepts:

* String
* Object
* Array
* Buffer

Example:

```js
res.send("Hello");
res.send({ name: "Pradeep" });
```

---

## 3.2 `res.json()`

Always JSON.

```js
res.json({ success: true });
```

Automatically sets:

```
Content-Type: application/json
```

---

## 3.3 `res.status()`

Sets HTTP status code.

```js
res.status(404).send("Not Found");
```

Chaining possible because it returns `res`.

---

## 3.4 `res.redirect()`

Redirects client.

```js
res.redirect('/login');
```

Optional status:

```js
res.redirect(301, '/new-url');
```

---

## 3.5 `res.sendFile()`

Sends file from server.

```js
const path = require('path');

res.sendFile(path.join(__dirname, 'index.html'));
```

---

## 3.6 `res.set()`

Sets custom headers.

```js
res.set('X-Custom-Header', 'MyValue');
```

---

## 3.7 `res.type()`

Sets Content-Type.

```js
res.type('application/json');
```

---

## 3.8 `res.end()`

Ends response manually.

Rarely needed in Express.

---

# 4️⃣ Headers Handling

---

## What Are Headers?

Metadata sent with request/response.

Examples:

* Content-Type
* Authorization
* Accept
* User-Agent

---

## Reading Headers

```js
req.headers
req.get('Authorization')
```

---

## Setting Headers

```js
res.set('Cache-Control', 'no-store');
```

---

# 5️⃣ Cookies

Cookies are stored in browser and sent automatically.

---

## Setting Cookie

```js
res.cookie('token', 'abc123', {
    httpOnly: true,
    secure: true
});
```

---

## Clearing Cookie

```js
res.clearCookie('token');
```

---

# 6️⃣ Content Negotiation

Client sends:

```
Accept: application/json
```

Server can check:

```js
req.accepts('json');
```

Or:

```js
res.format({
    'text/plain': () => res.send('Text'),
    'application/json': () => res.json({ message: 'JSON' })
});
```

---

# 7️⃣ Response Lifecycle

---

### Step 1: Status set

### Step 2: Headers set

### Step 3: Body written

### Step 4: Response ended

Once `res.send()` is called:

* Headers are locked
* Response is finalized

Trying to modify after that causes error:

```
Cannot set headers after they are sent
```

---

# 8️⃣ Status Codes (REST Proper Usage)

---

## 200 – OK

## 201 – Created

## 204 – No Content

## 400 – Bad Request

## 401 – Unauthorized

## 403 – Forbidden

## 404 – Not Found

## 500 – Internal Server Error

---

## Professional REST Pattern

```js
res.status(200).json({
    success: true,
    data: users
});
```

---

# 9️⃣ Advanced Response Patterns

---

## Conditional Response

```js
if (!data) {
    return res.status(404).json({
        success: false,
        message: "Not found"
    });
}
```

---

## Streaming (Intro Only)

```js
const fs = require('fs');

const stream = fs.createReadStream('file.txt');
stream.pipe(res);
```

---

# 🔟 Common Mistakes

---

❌ Sending multiple responses
❌ Forgetting return after error
❌ Not enabling `express.json()`
❌ Modifying headers after sending response
❌ Trusting raw `req.body` without validation

---

# 🧪 Exercises

---

### Exercise 1

Create route:

```
POST /login
```

* Check if `req.body.username` exists
* If not → 400
* Else → return 200 with JSON

---

### Exercise 2

Create route:

```
GET /profile
```

* Check `Authorization` header
* If missing → 401
* Else → return profile JSON

---

### Exercise 3

Create route that:

* Sets custom header `X-App-Version`
* Sends JSON response

---

# 🎯 After Module 5 You Must Master

* Internal structure of `req`
* Internal structure of `res`
* Header handling
* Cookie handling
* Proper status code usage
* Response lifecycle
* Content negotiation basics
* REST response structure patterns

---


