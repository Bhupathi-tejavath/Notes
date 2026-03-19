


---

# 📘 MODULE 10: Authentication & Authorization in Next.js



# **Authentication & Authorization in Next.js**

This module will not be superficial. We will examine:

* Authentication fundamentals
* Session vs JWT models
* Authentication in App Router
* Protecting routes
* Middleware-based guards
* Role-based access control
* Secure cookies
* CSRF protection
* Using NextAuth (Auth.js)
* Production-grade security architecture




---

# 1. Authentication vs Authorization

These two terms are often confused but are fundamentally different.

Authentication answers:

> Who are you?

Authorization answers:

> What are you allowed to do?

Example:

* User logs in with email/password → Authentication.
* Admin allowed to access `/admin` → Authorization.

Authentication must happen before authorization.

---

# 2. Authentication Models

There are two dominant models in web applications:

## 2.1 Session-Based Authentication

Flow:

1. User logs in.
2. Server verifies credentials.
3. Server creates session record in database.
4. Server sends session ID in cookie.
5. On future requests, cookie is validated.

Advantages:

* Simple.
* Server controls session lifecycle.
* Easy revocation.

Disadvantages:

* Requires session storage.
* Less scalable in distributed systems (unless using Redis).

---

## 2.2 JWT (Token-Based Authentication)

Flow:

1. User logs in.
2. Server signs JWT.
3. JWT sent to client.
4. Client sends JWT in cookie or header.
5. Server verifies signature.

Example JWT generation:

```tsx id="jwtgen1"
import jwt from "jsonwebtoken";

const token = jwt.sign(
  { userId: 1 },
  process.env.JWT_SECRET!,
  { expiresIn: "1h" }
);
```

Verification:

```tsx id="jwtverify1"
jwt.verify(token, process.env.JWT_SECRET!);
```

Advantages:

* Stateless.
* Scales easily.
* No DB lookup required for validation.

Disadvantages:

* Harder to revoke before expiry.
* Must protect secret carefully.

---

# 3. Where Authentication Runs in Next.js

In App Router, authentication logic can be placed in:

* Route Handlers
* Server Actions
* Middleware
* Server Components

Never perform authentication purely in Client Components.

Client-side checks are easily bypassed.

---

# 4. Secure Cookie Handling

Best practice: store tokens in HTTP-only cookies.

Example:

```tsx id="securecookie1"
import { NextResponse } from "next/server";

export async function POST() {
  const token = "signed_jwt_token";

  const response = NextResponse.json({ success: true });

  response.cookies.set("auth", token, {
    httpOnly: true,
    secure: true,
    sameSite: "strict",
    path: "/",
  });

  return response;
}
```

Important flags:

* httpOnly → Not accessible via JavaScript.
* secure → Only over HTTPS.
* sameSite → Prevent CSRF.
* path → Cookie scope.

---

# 5. Reading Authentication in Server Components

```tsx id="readcookie1"
import { cookies } from "next/headers";

export default function Dashboard() {
  const cookieStore = cookies();
  const token = cookieStore.get("auth");

  if (!token) {
    return <div>Unauthorized</div>;
  }

  return <div>Dashboard</div>;
}
```

Because Server Components run on server, cookies are accessible securely.

---

# 6. Protecting Routes with Middleware

Middleware runs before request hits route.

Example:

```tsx id="middlewareauth1"
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";

export function middleware(request: NextRequest) {
  const token = request.cookies.get("auth");

  if (!token) {
    return NextResponse.redirect(
      new URL("/login", request.url)
    );
  }

  return NextResponse.next();
}

export const config = {
  matcher: ["/dashboard/:path*"],
};
```

This protects all dashboard routes.

Middleware runs at edge by default.

---

# 7. Role-Based Access Control (RBAC)

Store role in token:

```tsx id="rolejwt1"
jwt.sign({ userId: 1, role: "admin" }, secret);
```

Middleware:

```tsx id="rolecheck1"
const payload = jwt.verify(token, secret);

if (payload.role !== "admin") {
  return NextResponse.redirect("/unauthorized");
}
```

Now access is role-based.

---

# 8. Authentication in Server Actions

Server Actions can enforce authentication:

```tsx id="serveractionauth1"
"use server";

import { cookies } from "next/headers";

export async function createPost(formData: FormData) {
  const token = cookies().get("auth");

  if (!token) {
    throw new Error("Unauthorized");
  }

  // Perform mutation
}
```

Server Actions run on server → secure by default.

---

# 9. CSRF Protection

CSRF = Cross-Site Request Forgery.

Occurs when malicious site triggers request on behalf of user.

Protection mechanisms:

* sameSite cookies
* CSRF tokens
* Double-submit cookie pattern

Example:

```tsx id="csrf1"
<input type="hidden" name="csrfToken" value={token} />
```

Validate token server-side.

NextAuth handles CSRF automatically.

---

# 10. NextAuth (Auth.js) Overview

NextAuth simplifies authentication.

Supports:

* Email/password
* OAuth (Google, GitHub)
* Credentials provider
* JWT or database sessions

Installation:

```bash id="nextauthinstall"
npm install next-auth
```

Basic configuration:

```tsx id="nextauthroute1"
// app/api/auth/[...nextauth]/route.ts

import NextAuth from "next-auth";
import GitHubProvider from "next-auth/providers/github";

const handler = NextAuth({
  providers: [
    GitHubProvider({
      clientId: process.env.GITHUB_ID!,
      clientSecret: process.env.GITHUB_SECRET!,
    }),
  ],
});

export { handler as GET, handler as POST };
```

This creates full authentication system.

---

# 11. Getting Session in Server Component (NextAuth)

```tsx id="getsession1"
import { getServerSession } from "next-auth";

export default async function Page() {
  const session = await getServerSession();

  if (!session) {
    return <div>Login required</div>;
  }

  return <div>Welcome {session.user?.name}</div>;
}
```

Secure and server-rendered.

---

# 12. Client-Side Session Access

```tsx id="usesession1"
"use client";

import { useSession } from "next-auth/react";

export default function Component() {
  const { data: session } = useSession();

  return <p>{session?.user?.email}</p>;
}
```

Used for UI personalization only.

Never trust client session for authorization.

---

# 13. Secure Authentication Architecture Pattern

Production-grade flow:

1. Login route handler verifies credentials.
2. Generate JWT.
3. Store in HTTP-only secure cookie.
4. Middleware verifies token for protected routes.
5. Server Components read token.
6. Role-based checks enforced server-side.
7. All mutations validated server-side.

Client only handles UI.

---

# 14. Common Security Mistakes

Storing JWT in localStorage
Trusting client-side role checks
Not validating input
Using insecure cookies in production
Exposing JWT secret in client bundle
Not handling token expiry

---

# 15. Authentication Strategy for Large Systems

For dashboard-style applications:

* Use JWT stored in HTTP-only cookie.
* Use middleware for route protection.
* Use role-based logic in Server Components.
* Use Server Actions for secure mutations.
* Use tag-based revalidation after updates.

---

# 16. Execution Flow in Protected Route

User visits `/dashboard`:

1. Middleware checks cookie.
2. If valid → continue.
3. Server Component renders.
4. Server reads cookie.
5. Data fetched.
6. Page streamed.
7. Client hydrates interactive parts.

Everything security-critical remains server-side.

---

# Understanding Check – Module 10

1. Explain the difference between authentication and authorization.
2. Compare session-based vs JWT-based authentication.
3. Why should JWT not be stored in localStorage?
4. Why must authorization checks happen server-side?
5. How does middleware protect routes?
6. What is CSRF and how does sameSite cookie help?
7. When should you use NextAuth instead of custom JWT?
8. Why is role-based access control important in large systems?

---

