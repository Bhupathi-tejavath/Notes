





---

# 📘 MODULE 11: Middleware Deep Dive


# **Middleware & Request Interception Architecture**

Middleware is not simply “auth protection.” It is a request interception layer that operates before routing resolution completes.

This module will cover:

* What middleware truly is
* How it integrates with the request lifecycle
* Edge runtime mechanics
* Redirects vs Rewrites
* Conditional routing
* Geo-based routing
* Performance implications
* Architectural patterns


---

# 1. What Is Middleware in Next.js?

Middleware is a function that runs **before a request reaches a route or page**.

It executes during the request lifecycle and can:

* Inspect request headers
* Inspect cookies
* Modify response
* Redirect requests
* Rewrite URLs
* Block access
* Inject headers

It runs at the **Edge Runtime by default**, meaning:

* It executes close to the user (CDN edge).
* It runs before server-side rendering.
* It has low latency.

Middleware sits between:

Client → CDN Edge → Middleware → Route Handler / Page

---

# 2. Where Middleware Lives

Middleware must be defined at the project root:

```plaintext id="4j3y8e"
middleware.ts
```

Basic example:

```tsx id="kwn5sk"
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";

export function middleware(request: NextRequest) {
  console.log("Middleware running");

  return NextResponse.next();
}
```

This runs for all routes by default.

---

# 3. Controlling Middleware Scope (Matcher)

You rarely want middleware to run globally.

Use config:

```tsx id="ot7f3l"
export const config = {
  matcher: ["/dashboard/:path*"],
};
```

Now middleware only runs for:

```plaintext id="s9ebg2"
/dashboard
/dashboard/settings
/dashboard/profile
```

This improves performance and reduces unnecessary execution.

---

# 4. Understanding the Request Lifecycle

When user requests `/dashboard`:

1. CDN receives request.
2. Middleware executes.
3. If middleware calls `NextResponse.next()` → continue.
4. If middleware redirects → redirect happens.
5. If middleware rewrites → route modified.
6. Finally, route handler or page renders.

Middleware does NOT render React components.

It intercepts and controls request flow.

---

# 5. Redirects vs Rewrites

These are often confused.

## Redirect

A redirect tells the browser to navigate to a new URL.

Example:

```tsx id="5k1yro"
return NextResponse.redirect(
  new URL("/login", request.url)
);
```

Browser URL changes to `/login`.

Used for:

* Authentication
* Canonical URL enforcement
* Permanent URL migration

---

## Rewrite

A rewrite changes route internally without changing URL.

Example:

```tsx id="xv9n7u"
return NextResponse.rewrite(
  new URL("/maintenance", request.url)
);
```

User sees `/dashboard` in address bar,
but content from `/maintenance` is served.

Used for:

* Feature flags
* A/B testing
* Localization
* Maintenance mode

---

# 6. Reading Cookies in Middleware

```tsx id="g14cnw"
export function middleware(request: NextRequest) {
  const token = request.cookies.get("auth");

  if (!token) {
    return NextResponse.redirect(
      new URL("/login", request.url)
    );
  }

  return NextResponse.next();
}
```

Because middleware runs at edge, cookie reading is efficient.

---

# 7. Reading Headers

```tsx id="0kn1pa"
const userAgent = request.headers.get("user-agent");
```

This enables:

* Device detection
* Bot detection
* API rate limiting logic
* Conditional routing

---

# 8. Geo-Based Routing

Edge middleware can detect location.

```tsx id="j37lcr"
const country = request.geo?.country;

if (country === "IN") {
  return NextResponse.rewrite(
    new URL("/india", request.url)
  );
}
```

Use cases:

* Regional pricing
* Localization
* Regulatory compliance
* Content personalization

---

# 9. Injecting Custom Headers

You can modify response headers:

```tsx id="j6ar1k"
const response = NextResponse.next();
response.headers.set("x-custom-header", "value");

return response;
```

Useful for:

* Security headers
* CSP enforcement
* Feature flags

---

# 10. Adding Security Headers

Example:

```tsx id="ysp3gk"
export function middleware(request: NextRequest) {
  const response = NextResponse.next();

  response.headers.set(
    "Content-Security-Policy",
    "default-src 'self'"
  );

  response.headers.set(
    "X-Frame-Options",
    "DENY"
  );

  return response;
}
```

Improves application security.

---

# 11. Rate Limiting Conceptually

Middleware can integrate with rate-limiting logic.

Example (conceptual):

```tsx id="hng7q2"
const ip = request.ip;

if (isRateLimited(ip)) {
  return new NextResponse("Too Many Requests", {
    status: 429,
  });
}
```

For production, use Redis or distributed store.

---

# 12. Edge Runtime Limitations

Middleware runs in Edge runtime, which means:

* No Node.js native modules
* No filesystem access
* No database drivers
* Limited memory

You cannot:

```tsx id="phl7ai"
// ❌ This will fail
import fs from "fs";
```

Middleware must remain lightweight.

---

# 13. Performance Considerations

Middleware runs on every matched request.

Therefore:

* Avoid heavy computations.
* Avoid external API calls.
* Avoid blocking logic.
* Avoid database queries.

Keep middleware:

* Fast
* Deterministic
* Stateless

---

# 14. Combining Middleware with Server Components

Middleware:

* Handles request validation.
* Performs early redirection.

Server Component:

* Handles data rendering.
* Performs role-based checks.

Layered security approach:

Middleware → coarse-grained protection
Server Component → fine-grained authorization

---

# 15. Feature Flags with Middleware

Example:

```tsx id="jz8zty"
if (request.cookies.get("betaUser")) {
  return NextResponse.rewrite(
    new URL("/beta-dashboard", request.url)
  );
}
```

Allows selective rollout of features.

---

# 16. Multi-Tenant Architecture Pattern

Example domain-based routing:

```tsx id="u8x3j1"
const host = request.headers.get("host");

if (host?.startsWith("admin.")) {
  return NextResponse.rewrite(
    new URL("/admin", request.url)
  );
}
```

Enables:

* Subdomain-based apps
* Tenant-based systems
* White-label solutions

---

# 17. Execution Order Clarification

Important:

Middleware runs BEFORE:

* Server Components
* Route Handlers
* Server Actions

It is the earliest interception point.

---

# 18. Production Middleware Architecture

For secure applications:

Middleware handles:

* Authentication token presence
* Basic validation
* Redirect logic
* Geo restrictions
* Bot filtering

Server Components handle:

* Role verification
* Database checks
* Data fetching

Never rely solely on middleware for deep authorization.

---

# Common Mistakes

Using database inside middleware
Performing heavy logic in middleware
Forgetting matcher configuration
Using rewrite when redirect is needed
Exposing sensitive data via headers

---

# Understanding Check – Module 11

1. At what stage in the request lifecycle does middleware execute?
2. What is the difference between rewrite and redirect?
3. Why can middleware not use Node.js modules?
4. Why should middleware logic be lightweight?
5. How does middleware improve security?
6. How can middleware support multi-tenant architecture?
7. Why should authorization still be enforced in Server Components?

---
