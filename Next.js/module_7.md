



---

# 📘 MODULE 7: Route Handlers & Backend Architecture in Next.js

This module covers:

* Route Handlers (modern API layer)
* HTTP methods handling
* Request/Response abstraction
* JSON and form handling
* Middleware basics
* Database integration
* Edge vs Node runtime
* Cookies and headers
* File uploads
* REST API design inside Next.js

---

# 1. The Concept of Route Handlers

In the App Router, backend endpoints are defined using **Route Handlers**.

They replace the old `pages/api` approach.

A route handler is defined using:

```plaintext
app/api/routeName/route.ts
```

Example structure:

```plaintext
app/
  api/
    users/
      route.ts
```

This creates endpoint:

```
/api/users
```

Route handlers allow handling HTTP requests directly within the Next.js project.

They execute on the server only.

---

# 2. Basic GET Route Handler

Example:

```tsx
// app/api/users/route.ts

export async function GET() {
  return Response.json({ message: "Users API Working" });
}
```

Visiting:

```
http://localhost:3000/api/users
```

Returns:

```json
{
  "message": "Users API Working"
}
```

Important:

* The function name must match HTTP method.
* You return a standard Web Response object.

---

# 3. Handling Multiple HTTP Methods

You can define:

* GET
* POST
* PUT
* DELETE
* PATCH

Example:

```tsx
export async function POST(request: Request) {
  const body = await request.json();

  return Response.json({
    received: body
  });
}
```

Now POST to:

```
/api/users
```

With JSON body.

---

# 4. The Request Object

The `Request` object follows Web Fetch API standard.

You can extract:

JSON body:

```tsx
const data = await request.json();
```

Form data:

```tsx
const formData = await request.formData();
```

URL parameters:

```tsx
const { searchParams } = new URL(request.url);
const id = searchParams.get("id");
```

Headers:

```tsx
const token = request.headers.get("authorization");
```

This is not Express — it uses Web standard Request API.

---

# 5. Returning Responses

Use:

```tsx
return new Response("Hello");
```

Or:

```tsx
return Response.json({ data: "value" });
```

Custom status:

```tsx
return new Response("Unauthorized", { status: 401 });
```

Custom headers:

```tsx
return new Response("OK", {
  status: 200,
  headers: {
    "Content-Type": "text/plain"
  }
});
```

---

# 6. Dynamic Route Handlers

You can create dynamic API routes.

Example:

```plaintext
app/api/users/[id]/route.ts
```

```tsx
export async function GET(
  request: Request,
  { params }: { params: { id: string } }
) {
  return Response.json({ userId: params.id });
}
```

Request:

```
/api/users/123
```

Response:

```json
{
  "userId": "123"
}
```

---

# 7. Connecting to a Database

Example using MySQL:

Install:

```bash
npm install mysql2
```

Example:

```tsx
import mysql from "mysql2/promise";

export async function GET() {
  const connection = await mysql.createConnection({
    host: "localhost",
    user: "root",
    password: "password",
    database: "test"
  });

  const [rows] = await connection.execute("SELECT * FROM users");

  return Response.json(rows);
}
```

Important production advice:

* Do not create new connection per request.
* Use connection pooling.

---

# 8. Using Environment Variables Securely

Store DB credentials in:

```
.env.local
```

Example:

```
DATABASE_URL=mysql://root:password@localhost:3306/test
```

Access inside route handler:

```tsx
process.env.DATABASE_URL
```

Never expose DB credentials to client.

---

# 9. Edge Runtime vs Node Runtime

Next.js supports two runtimes:

### Node.js Runtime (Default)

* Full Node APIs
* Database drivers
* Filesystem access
* Larger cold start

### Edge Runtime

Enable:

```tsx
export const runtime = "edge";
```

Edge limitations:

* No native Node modules
* No filesystem
* Limited APIs

Edge is suitable for:

* Lightweight auth checks
* Geo-based routing
* Middleware-like logic

For database-heavy operations → use Node runtime.

---

# 10. Middleware (Concept Introduction)

Middleware runs before request reaches route.

Create:

```plaintext
middleware.ts
```

Example:

```tsx
import { NextResponse } from "next/server";

export function middleware(request) {
  if (!request.cookies.get("auth")) {
    return NextResponse.redirect(new URL("/login", request.url));
  }

  return NextResponse.next();
}
```

This runs before every request (or scoped via config).

---

# 11. Handling Cookies

Read cookies:

```tsx
import { cookies } from "next/headers";

export async function GET() {
  const cookieStore = cookies();
  const token = cookieStore.get("auth");

  return Response.json({ token });
}
```

Set cookies:

```tsx
import { NextResponse } from "next/server";

export async function POST() {
  const response = NextResponse.json({ success: true });

  response.cookies.set("auth", "tokenvalue");

  return response;
}
```

---

# 12. Handling Headers

```tsx
import { headers } from "next/headers";

const headerList = headers();
const userAgent = headerList.get("user-agent");
```

Using headers makes route dynamic.

---

# 13. File Upload Handling

Example:

```tsx
export async function POST(request: Request) {
  const formData = await request.formData();
  const file = formData.get("file");

  return Response.json({ fileName: file.name });
}
```

For production:

* Store files in cloud storage (S3, Cloudinary)
* Avoid filesystem storage in serverless environments

---

# 14. REST API Design Principles in Next.js

Example REST structure:

```
GET     /api/users
GET     /api/users/1
POST    /api/users
PUT     /api/users/1
DELETE  /api/users/1
```

Follow REST conventions:

* Use nouns
* Use proper HTTP status codes
* Return structured JSON
* Handle validation errors properly

Example validation response:

```json
{
  "error": "Email is required"
}
```

Status:

```
400 Bad Request
```

---

# 15. Authentication Handling Example (JWT)

```tsx
import jwt from "jsonwebtoken";

export async function POST(request: Request) {
  const { username } = await request.json();

  const token = jwt.sign(
    { username },
    process.env.JWT_SECRET!,
    { expiresIn: "1h" }
  );

  return Response.json({ token });
}
```

Secure token in HTTP-only cookie.

---

# 16. API Performance Considerations

Avoid:

* Heavy computation inside route handler
* Creating DB connection per request
* Blocking synchronous operations

Prefer:

* Connection pooling
* Async non-blocking calls
* Caching when possible

---

# 17. Internal Execution Flow

When client calls `/api/users`:

1. Next.js matches route handler.
2. Executes in Node or Edge runtime.
3. Returns Response object.
4. Sends back JSON.

This does not involve React rendering.

---

# 18. Architectural Perspective

Next.js backend layer is ideal for:

* Small-to-medium apps
* BFF (Backend for Frontend) pattern
* Authentication
* Lightweight APIs

For large-scale microservices:

* Use separate backend service
* Or combine with Next.js as frontend gateway

---

# Common Mistakes

Using Node-only modules in Edge runtime
Not handling errors properly
Exposing sensitive environment variables
Blocking event loop
Not validating request body

---

# Understanding Check – Module 7

1. How are Route Handlers different from Express routes?
2. Why does Next.js use Web standard Request/Response instead of Node req/res?
3. When should you use Edge runtime?
4. Why is connection pooling important?
5. How do dynamic API routes receive parameters?
6. Why must sensitive data remain server-side?
7. How does middleware differ from route handlers?

---


