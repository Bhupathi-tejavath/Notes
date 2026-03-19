




---

# 📘 MODULE 5: Rendering Model & Data Fetching


This module explains:

* Server Components vs Client Components
* The rendering lifecycle
* Data fetching mechanics
* Caching behavior
* Static vs Dynamic rendering
* Revalidation
* Streaming
* Suspense
* How Next.js optimizes automatically
---

# 1. The Rendering Philosophy of Next.js

Traditional React renders in the browser.

Next.js introduces a hybrid execution model where components may run:

* On the server
* In the browser
* At build time
* At request time

This is not just an optimization — it is an architectural shift.

The fundamental idea:

> Render as much as possible on the server. Send minimal JavaScript to the client.

This reduces:

* Bundle size
* Initial load time
* Client computation
* SEO issues

---

# 2. Server Components (Default in App Router)

In the App Router, every component is a Server Component by default.

Example:

```tsx
export default function Page() {
  return <h1>Hello</h1>;
}
```

This runs entirely on the server.

Key Characteristics of Server Components:

* No JavaScript is sent to browser for them.
* They can directly access databases.
* They can use environment variables.
* They cannot use React hooks like `useState`.
* They cannot use browser APIs.

This is extremely powerful.

Example with database access:

```tsx
export default async function Page() {
  const users = await fetch("https://api.example.com/users")
    .then(res => res.json());

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

This code executes on the server, not in the browser.

No API layer required if data is directly accessible.

---

# 3. Why Server Components Matter

Server Components:

* Reduce client-side JavaScript.
* Improve performance.
* Improve SEO.
* Improve security (no DB exposure).
* Allow direct backend integration.

In your full-stack architecture mindset, this removes the need for:

React → API → Backend → Database

Instead:

React (Server Component) → Database

Cleaner architecture.

---

# 4. Client Components

If a component needs:

* useState
* useEffect
* Event handlers
* Browser APIs
* useRouter

Then it must be marked as a Client Component.

You do this by adding at the top:

```tsx
"use client";
```

Example:

```tsx
"use client";

import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  );
}
```

Now this component:

* Runs in browser
* Is included in client bundle
* Hydrates after HTML loads

---

# 5. Server vs Client Component Comparison

Server Component:

* Executes on server
* No hydration required
* No browser APIs
* Smaller bundle
* Better for data-heavy UI

Client Component:

* Executes in browser
* Hydrated
* Supports interactivity
* Larger bundle
* Needed for UI interaction

Architectural Rule:

> Default to Server Components. Use Client Components only when necessary.

---

# 6. Combining Server and Client Components

You can embed Client Components inside Server Components.

Example:

```tsx
import Counter from "./Counter";

export default function Page() {
  return (
    <div>
      <h1>Dashboard</h1>
      <Counter />
    </div>
  );
}
```

Here:

* Page is Server Component.
* Counter is Client Component.
* Only Counter JS is sent to browser.

This granular bundling is one of Next.js’s most powerful optimizations.

---

# 7. Data Fetching in Server Components

In the App Router, data fetching is done using standard `fetch()`.

Example:

```tsx
export default async function Page() {
  const res = await fetch("https://api.example.com/posts");
  const posts = await res.json();

  return <div>{posts.length}</div>;
}
```

This fetch runs on server.

No `getServerSideProps` required.

---

# 8. Fetch Caching Behavior

By default, Next.js caches fetch requests in Server Components.

Default behavior:

* Static rendering
* Cache result
* Reuse across requests

This means if you write:

```tsx
await fetch("https://api.example.com/posts");
```

It may behave like static generation unless specified otherwise.

---

# 9. Forcing Dynamic Rendering

To disable caching:

```tsx
await fetch("https://api.example.com/posts", {
  cache: "no-store",
});
```

This ensures:

* Data fetched on every request
* Equivalent to SSR

Alternatively:

```tsx
export const dynamic = "force-dynamic";
```

This forces route to render dynamically.

---

# 10. Static Rendering

Static rendering happens when:

* No dynamic fetch
* No cookies
* No headers usage
* No dynamic flags

Next.js pre-renders page at build time.

Example:

```tsx
export default async function Page() {
  const res = await fetch("https://api.example.com/posts");
  const data = await res.json();
  return <div>{data.length}</div>;
}
```

If no dynamic signals are used, this is static.

---

# 11. Revalidation (ISR)

Revalidation allows periodic regeneration.

Example:

```tsx
export const revalidate = 60;
```

This means:

* Generate page at build.
* Regenerate in background every 60 seconds.

ISR is ideal for:

* Blogs
* News
* Product catalogs

---

# 12. On-Demand Revalidation

You can revalidate specific paths programmatically.

```tsx
import { revalidatePath } from "next/cache";

revalidatePath("/blog");
```

Useful in Server Actions after mutation.

---

# 13. Streaming

Streaming allows partial UI rendering.

Instead of waiting for all data, Next.js streams segments as they become ready.

Example with Suspense:

```tsx
import { Suspense } from "react";

export default function Page() {
  return (
    <div>
      <h1>Dashboard</h1>
      <Suspense fallback={<p>Loading stats...</p>}>
        <Stats />
      </Suspense>
    </div>
  );
}
```

Stats component:

```tsx
async function Stats() {
  const data = await fetch("...");
  return <div>{data.value}</div>;
}
```

Now:

* Header renders immediately
* Stats loads later
* Improves perceived performance

---

# 14. Loading UI (Automatic Suspense)

Using:

```plaintext
loading.tsx
```

Next.js automatically wraps route in Suspense boundary.

This integrates tightly with streaming.

---

# 15. How Rendering Is Decided

Next.js analyzes:

* fetch options
* cookies usage
* headers usage
* dynamic flags

Then classifies route as:

* Static
* Dynamic
* ISR
* Fully dynamic

This happens at build time.

---

# 16. Hydration in Client Components

Server Components render HTML only.

Client Components:

1. Receive HTML.
2. Load JS bundle.
3. Hydrate.
4. Attach event listeners.

Only client components hydrate.

This dramatically reduces JS payload.

---

# 17. Architectural Insight

The App Router rendering model is built on:

React Server Components + Suspense + Streaming + Intelligent Caching.

This enables:

* Near-zero client JS for static pages.
* Hybrid rendering per component.
* Fine-grained optimization.

In large systems, this enables:

* Fast dashboards
* SEO-friendly dynamic apps
* Efficient real-time features

---

# Common Mistakes

Marking entire app as `"use client"`
Using `no-store` everywhere unnecessarily
Not understanding default fetch caching
Using client components for data fetching unnecessarily

---

# Understanding Check – Module 5

1. Why are Server Components the default in App Router?
2. What happens if you use useState inside a Server Component?
3. Explain the difference between `cache: "no-store"` and `revalidate`.
4. How does streaming improve user experience?
5. Why does Next.js minimize client-side JavaScript?
6. When would you intentionally force dynamic rendering?
7. How does Next.js determine whether a page is static or dynamic?

---

