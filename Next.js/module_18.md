
---

# 📘 MODULE 18: Security Best Practices in Next.js

# **Security Engineering in Next.js**

Security is not a feature you “add later.” It must be embedded in architecture from the beginning.

This module will cover:

* XSS (Cross-Site Scripting)
* CSRF (Cross-Site Request Forgery)
* Content Security Policy (CSP)
* Secure headers
* Rate limiting
* Environment variable security
* API protection strategies
* Secure authentication patterns
* Edge-level protection
* Common security anti-patterns


---

# 1. Security Philosophy in Full-Stack React Frameworks

Next.js combines:

* Server-side execution
* Client-side interactivity
* API routes
* Server actions

This increases attack surface.

Security must protect:

* Data
* Authentication tokens
* APIs
* Database
* Infrastructure
* User input

Rule:

> Never trust client input. Always validate on server.

---

# 2. XSS (Cross-Site Scripting)

XSS occurs when malicious JavaScript is injected into your site and executed in the user’s browser.

Example attack:

User submits:

```html id="xss1"
<script>alert('hacked')</script>
```

If rendered without escaping, it executes.

---

## 2.1 Why Next.js Is Safer by Default

React automatically escapes strings:

```tsx id="xss2"
export default function Page() {
  const input = "<script>alert('x')</script>";
  return <div>{input}</div>;
}
```

This renders as text, not executable script.

However, danger arises when using:

```tsx id="xss3"
dangerouslySetInnerHTML
```

Example:

```tsx id="xss4"
<div
  dangerouslySetInnerHTML={{
    __html: userInput
  }}
/>
```

This bypasses React escaping.

Only use when content is sanitized.

---

## 2.2 Sanitizing HTML

Use libraries like DOMPurify:

```bash id="xss5"
npm install dompurify
```

Example:

```tsx id="xss6"
import DOMPurify from "dompurify";

const clean = DOMPurify.sanitize(userInput);
```

Always sanitize user-generated HTML.

---

# 3. CSRF (Cross-Site Request Forgery)

CSRF occurs when an attacker tricks a logged-in user into performing unwanted actions.

Example:

User logged into your site.

Malicious site sends hidden form to:

```plaintext id="csrf1"
/api/delete-account
```

If no protection, action executes.

---

## 3.1 Protecting Against CSRF

Use:

* sameSite cookies
* CSRF tokens
* Double-submit token pattern

Example secure cookie:

```tsx id="csrf2"
response.cookies.set("auth", token, {
  httpOnly: true,
  secure: true,
  sameSite: "strict"
});
```

sameSite prevents cross-site requests from sending cookie.

---

# 4. Content Security Policy (CSP)

CSP restricts which resources can be loaded.

Example header:

```tsx id="csp1"
response.headers.set(
  "Content-Security-Policy",
  "default-src 'self'; script-src 'self'"
);
```

This prevents:

* Loading scripts from unknown sources
* Inline script execution

CSP reduces XSS risk.

Implement via middleware.

---

# 5. Secure HTTP Headers

Important security headers:

* X-Frame-Options
* X-Content-Type-Options
* Referrer-Policy
* Strict-Transport-Security

Example middleware:

```tsx id="sec1"
export function middleware(request) {
  const response = NextResponse.next();

  response.headers.set("X-Frame-Options", "DENY");
  response.headers.set("X-Content-Type-Options", "nosniff");
  response.headers.set("Referrer-Policy", "strict-origin");

  return response;
}
```

These mitigate:

* Clickjacking
* MIME sniffing attacks
* Referrer leakage

---

# 6. Environment Variable Security

Never expose secrets to client.

Bad:

```env id="envbad"
NEXT_PUBLIC_DB_PASSWORD=secret
```

Good:

```env id="envgood"
DATABASE_PASSWORD=secret
```

Only variables prefixed with NEXT_PUBLIC_ are exposed to client bundle.

Never commit `.env.local` to version control.

---

# 7. Protecting API Routes

Always validate input:

Example:

```tsx id="api1"
export async function POST(request: Request) {
  const body = await request.json();

  if (!body.email || typeof body.email !== "string") {
    return new Response("Invalid input", { status: 400 });
  }

  return Response.json({ success: true });
}
```

Never trust client data blindly.

---

# 8. Rate Limiting

Prevents abuse or brute-force attacks.

Basic concept:

* Track IP request count.
* Block if threshold exceeded.

Example conceptual middleware:

```tsx id="rate1"
if (isRateLimited(request.ip)) {
  return new Response("Too Many Requests", {
    status: 429
  });
}
```

In production:

* Use Redis or distributed store.
* Do not use in-memory counters in serverless.

---

# 9. SQL Injection Prevention

Never concatenate raw input in SQL.

Bad:

```ts id="sqlbad"
const query = "SELECT * FROM users WHERE id = " + userInput;
```

Good:

```ts id="sqlgood"
await connection.execute(
  "SELECT * FROM users WHERE id = ?",
  [userInput]
);
```

Always use parameterized queries.

---

# 10. Authentication Security Best Practices

Use:

* HTTP-only cookies
* JWT with expiry
* Strong secret key
* Refresh token strategy
* Role-based authorization
* Server-side validation

Never:

* Store JWT in localStorage.
* Trust client role checks.

---

# 11. Protecting Server Actions

Server Actions run on server, but still:

* Validate form input.
* Check authentication.
* Check authorization.
* Sanitize strings.

Example:

```tsx id="srvact1"
"use server";

import { cookies } from "next/headers";

export async function deleteUser(id: string) {
  const token = cookies().get("auth");

  if (!token) throw new Error("Unauthorized");

  if (!id.match(/^\d+$/)) {
    throw new Error("Invalid ID");
  }

  await db.deleteUser(id);
}
```

---

# 12. Avoiding Sensitive Data Leakage

Never return:

* Password hashes
* Internal IDs
* Secret keys
* Database errors

Bad:

```tsx id="leak1"
return Response.json(error);
```

Good:

```tsx id="leak2"
return new Response("Internal Server Error", {
  status: 500
});
```

---

# 13. Handling File Upload Security

Validate:

* File size
* File type
* File extension
* Scan for malware if necessary

Never trust MIME type from client.

---

# 14. Preventing Open Redirects

Validate redirect URLs:

Bad:

```tsx id="redirbad"
return NextResponse.redirect(new URL(userInput));
```

Good:

Validate domain before redirecting.

---

# 15. Securing Multi-Tenant Apps

Always enforce:

* Tenant ID in DB queries.
* Token-based tenant verification.
* Never rely only on URL for tenant isolation.

Example:

```ts id="tenant1"
SELECT * FROM users WHERE tenant_id = ?
```

---

# 16. Logging & Monitoring for Security

Monitor:

* Failed login attempts
* High request rates
* Suspicious patterns
* Error spikes

Use:

* Sentry
* Datadog
* Cloud logs

Security is ongoing monitoring, not one-time setup.

---

# 17. Production Security Checklist

* HTTPS enabled
* HTTP-only cookies
* CSP configured
* Input validation enforced
* Rate limiting enabled
* DB parameterized queries
* No secrets exposed
* Robots.txt configured
* Error messages sanitized

---

# 18. Security Anti-Patterns

Using localStorage for tokens
Disabling sameSite cookies
Skipping input validation
Trusting client data
Returning stack traces
Hardcoding secrets in repo
Overusing dangerouslySetInnerHTML

---

# Understanding Check – Module 18

1. Why is dangerouslySetInnerHTML risky?
2. How does sameSite cookie help prevent CSRF?
3. Why should JWT not be stored in localStorage?
4. What is SQL injection and how do parameterized queries prevent it?
5. Why must input always be validated server-side?
6. How does CSP reduce XSS risk?
7. Why should error messages not expose stack traces?

---

