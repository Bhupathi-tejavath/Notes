
---

# 📘 MODULE 19: Migration & Interoperability

# **Migration & Interoperability in Next.js**

This module focuses on real-world scenarios where:

* You already have a React SPA.
* You already have a backend.
* You are migrating from Pages Router to App Router.
* You need hybrid rendering.
* You are incrementally adopting Next.js.

This is not theoretical. Migration planning determines project risk, downtime, SEO continuity, and developer productivity.

---

# 1. Migrating from React SPA to Next.js

Most developers start with:

```bash
npx create-react-app
```

Or Vite-based React SPA.

Problem with SPA:

* Poor SEO
* Large initial JS bundle
* Manual routing
* No built-in SSR
* No backend layer

Migration goal:

* Introduce SSR
* Improve SEO
* Optimize performance
* Consolidate frontend + backend

---

## 1.1 Architectural Differences

React SPA:

Browser → JS bundle → React renders → Fetch API → Backend

Next.js:

Browser → Server renders HTML → Hydration → Client interaction

Migration requires:

* Replacing React Router
* Replacing fetch logic patterns
* Moving data fetching to server components
* Removing global useEffect-based fetching

---

## 1.2 Step-by-Step Migration Strategy

Step 1: Create Next.js app.

Step 2: Move reusable components.

Step 3: Replace routing:

React Router:

```tsx
<Route path="/dashboard" element={<Dashboard />} />
```

Next.js:

```plaintext
app/dashboard/page.tsx
```

Step 4: Convert data fetching.

React SPA:

```tsx
useEffect(() => {
  fetch("/api/posts")
    .then(res => res.json())
    .then(setPosts);
}, []);
```

Next.js Server Component:

```tsx
export default async function Page() {
  const posts = await fetch("/api/posts")
    .then(res => res.json());

  return <div>{posts.length}</div>;
}
```

Step 5: Remove unnecessary client components.

Step 6: Implement metadata API.

---

## 1.3 Handling Client-Only Libraries

Some libraries depend on browser APIs.

Example:

```tsx
window.localStorage
```

Wrap them inside client components:

```tsx
"use client";
```

Or dynamic import with no SSR:

```tsx
const Map = dynamic(() => import("./Map"), {
  ssr: false,
});
```

---

# 2. Migrating from Pages Router to App Router

Pages Router structure:

```plaintext
pages/
  index.tsx
  dashboard.tsx
  api/
```

Uses:

* getServerSideProps
* getStaticProps
* getStaticPaths

App Router replaces these with:

* Server Components
* generateStaticParams
* Route Handlers
* Layout system

---

## 2.1 Converting getServerSideProps

Pages Router:

```tsx
export async function getServerSideProps() {
  const res = await fetch("...");
  return { props: { data: await res.json() } };
}
```

App Router:

```tsx
export default async function Page() {
  const data = await fetch("...", { cache: "no-store" })
    .then(res => res.json());

  return <div>{data.length}</div>;
}
```

No wrapper function needed.

---

## 2.2 Converting getStaticProps

Pages Router:

```tsx
export async function getStaticProps() {
  return { props: { data }, revalidate: 60 };
}
```

App Router:

```tsx
export const revalidate = 60;
```

Data fetched directly inside component.

---

## 2.3 Converting getStaticPaths

Pages Router:

```tsx
export async function getStaticPaths() {
  return {
    paths: [...],
    fallback: false
  };
}
```

App Router:

```tsx
export async function generateStaticParams() {
  return [...];
}
```

Simpler and more intuitive.

---

# 3. Hybrid Router (Coexistence Strategy)

Next.js allows:

* pages/
* app/

to coexist temporarily.

Useful for gradual migration.

Routing priority:

App Router takes precedence for matching routes.

This allows incremental migration without breaking app.

---

# 4. Integrating with External Backend

If you already have:

* Express server
* Spring Boot backend
* Django API
* Microservices architecture

Next.js can act as:

Frontend-only client OR
Backend-for-Frontend gateway.

---

## 4.1 Direct Integration

Server Component:

```tsx
export default async function Page() {
  const data = await fetch("https://api.example.com/posts");
  const posts = await data.json();

  return <div>{posts.length}</div>;
}
```

Avoid exposing backend directly to client.

---

## 4.2 Auth Forwarding Pattern

```tsx
export async function GET(request: Request) {
  const token = request.headers.get("authorization");

  const res = await fetch("https://backend/api", {
    headers: { Authorization: token }
  });

  return Response.json(await res.json());
}
```

Next.js becomes secure proxy.

---

# 5. Migrating REST to Server Actions

Before:

Client → fetch → API route → DB

After:

Client → Server Action → DB

Steps:

* Extract mutation logic.
* Replace fetch calls with action.
* Add revalidatePath.

Simplifies mutation layer.

---

# 6. Migrating Large Applications Safely

Strategy:

* Migrate marketing pages first.
* Keep dashboard under Pages Router.
* Gradually move sections.
* Test thoroughly.
* Monitor performance.

Avoid big-bang migration.

---

# 7. Hybrid Rendering Strategy

Example:

Marketing pages → Static
Blog → ISR
Dashboard → Dynamic
Admin panel → Dynamic + role-based

Mix rendering strategies intentionally.

---

# 8. Migrating Authentication System

If SPA used localStorage JWT:

Migration:

* Move token to HTTP-only cookie.
* Use middleware for protection.
* Validate on server components.

Never keep SPA insecure token storage.

---

# 9. Data Layer Migration

In SPA:

Client fetches everything.

In Next.js:

* Fetch in Server Components.
* Use caching intelligently.
* Use tag-based revalidation.

This shifts load from client to server/CDN.

---

# 10. SEO Preservation During Migration

Ensure:

* URL structure preserved.
* 301 redirects for changed paths.
* Metadata preserved.
* Sitemap updated.
* No broken links.

Use Next.js redirect config:

```javascript
async redirects() {
  return [
    {
      source: "/old-page",
      destination: "/new-page",
      permanent: true,
    },
  ];
}
```

---

# 11. Incremental Adoption Strategy

Large enterprise pattern:

Phase 1: Static pages in Next.js
Phase 2: Dashboard migration
Phase 3: API integration
Phase 4: Server Actions adoption
Phase 5: Performance optimization

Gradual transition reduces risk.

---

# 12. Interoperability with Other Frontends

Next.js can coexist with:

* Mobile apps consuming same API.
* Separate admin app.
* Microfrontend architecture.

Use:

* Shared API contracts.
* Shared types.
* Shared validation schemas.

---

# 13. Migration Anti-Patterns

Rewriting everything at once
Breaking SEO URLs
Not testing production build
Leaving legacy routing logic
Keeping SPA fetch patterns unnecessarily
Not updating authentication security

---

# Understanding Check – Module 19

1. What is the primary architectural difference between React SPA and Next.js?
2. How do you replace getServerSideProps in App Router?
3. Why should migration be incremental instead of big-bang?
4. How can Next.js act as Backend-for-Frontend?
5. Why should JWT storage move from localStorage to HTTP-only cookies?
6. How does generateStaticParams replace getStaticPaths?
7. What risks must be managed during SEO migration?

---


