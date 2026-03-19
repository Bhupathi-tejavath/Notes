
---

# 📘 MODULE 3: Routing System (App Router)

---

## 1. File-System Based Routing

Next.js uses the file system to define routes. Unlike React Router, where you manually declare route mappings, Next.js interprets folder structure as URL structure.

This approach is deterministic: the directory hierarchy inside the `app/` folder directly defines the URL hierarchy of the application.

For example:

```
app/
  page.tsx
  about/
    page.tsx
  blog/
    page.tsx
```

This produces:

```
/           → app/page.tsx
/about      → app/about/page.tsx
/blog       → app/blog/page.tsx
```

The routing mechanism works by scanning the `app` directory during build time. It constructs a route manifest mapping URL paths to components.

This eliminates:

* Manual route configuration
* Route duplication
* Mismatched paths
* Boilerplate router setup

Internally, Next.js builds a route tree and determines which components are server components or client components.

---

## 2. The app/ Directory

The `app/` directory is the root of the App Router architecture.

It contains:

* `page.tsx`
* `layout.tsx`
* Nested folders
* Route groups
* Loading and error boundaries
* API route handlers

Unlike the older `pages/` directory, the `app/` directory introduces:

* Server Components by default
* Nested layouts
* Streaming
* Built-in Suspense integration

The App Router is fundamentally built around React Server Components.

---

## 3. page.tsx – Route Entry Point

A `page.tsx` file defines the actual UI rendered when a route is accessed.

Example:

```
app/dashboard/page.tsx
```

```tsx
export default function DashboardPage() {
  return (
    <main>
      <h1>Dashboard</h1>
    </main>
  );
}
```

When visiting:

```
/dashboard
```

Next.js renders this component.

Important:

* A folder without `page.tsx` is not accessible directly as a route.
* `page.tsx` must export a default React component.

---

## 4. layout.tsx – Persistent Layout

Layouts define UI that persists across navigation.

Example:

```
app/layout.tsx
```

```tsx
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html>
      <body>
        <header>My App</header>
        {children}
        <footer>Footer</footer>
      </body>
    </html>
  );
}
```

This layout wraps every page in the app.

The `children` prop represents nested route content.

Nested layout example:

```
app/dashboard/layout.tsx
```

```tsx
export default function DashboardLayout({ children }) {
  return (
    <div>
      <aside>Sidebar</aside>
      <section>{children}</section>
    </div>
  );
}
```

Now:

```
/dashboard
/dashboard/settings
```

Both use the DashboardLayout.

This enables:

* Persistent navigation bars
* Sidebar layouts
* Shared UI across sections

---

## 5. Nested Routes

Routing supports deep nesting.

Example structure:

```
app/
  dashboard/
    page.tsx
    settings/
      page.tsx
```

Routes:

```
/dashboard
/dashboard/settings
```

Nested folders define nested URL segments.

Each folder may define its own layout and page.

Rendering order:

Root layout → Dashboard layout → Settings page

---

## 6. Dynamic Routes

Dynamic segments allow parameterized URLs.

Folder naming convention:

```
[parameterName]
```

Example:

```
app/blog/[slug]/page.tsx
```

This matches:

```
/blog/first-post
/blog/nextjs-routing
```

Accessing params:

```tsx
export default function BlogPost({
  params,
}: {
  params: { slug: string };
}) {
  return <h1>{params.slug}</h1>;
}
```

Here, `params.slug` equals the dynamic segment.

---

## 7. Catch-All Routes

Catch-all routes match multiple segments.

```
[...slug]
```

Example:

```
app/docs/[...slug]/page.tsx
```

Matches:

```
/docs/a
/docs/a/b
/docs/a/b/c
```

Accessed as:

```tsx
export default function DocsPage({
  params,
}: {
  params: { slug: string[] };
}) {
  return <div>{params.slug.join(" / ")}</div>;
}
```

Optional catch-all:

```
[[...slug]]
```

This matches:

```
/docs
/docs/a
/docs/a/b
```

---

## 8. Linking Between Pages

Next.js provides optimized navigation via `next/link`.

Example:

```tsx
import Link from "next/link";

export default function Home() {
  return (
    <Link href="/dashboard">
      Go to Dashboard
    </Link>
  );
}
```

Key features:

* Client-side navigation
* Prefetching
* No full page reload
* Faster transitions

Prefetching happens automatically in viewport.

---

## 9. Programmatic Navigation

Use `useRouter` from `next/navigation`.

This is only usable in client components.

```tsx
"use client";

import { useRouter } from "next/navigation";

export default function Button() {
  const router = useRouter();

  return (
    <button onClick={() => router.push("/dashboard")}>
      Go
    </button>
  );
}
```

Methods include:

* push()
* replace()
* back()
* refresh()

---

## 10. Loading UI (loading.tsx)

Next.js allows per-route loading states.

Example:

```
app/dashboard/loading.tsx
```

```tsx
export default function Loading() {
  return <p>Loading...</p>;
}
```

This appears while server components fetch data.

Internally, this uses React Suspense.

---

## 11. Error Handling (error.tsx)

Per-route error boundaries:

```
app/dashboard/error.tsx
```

```tsx
"use client";

export default function Error({
  error,
  reset,
}: {
  error: Error;
  reset: () => void;
}) {
  return (
    <div>
      <p>{error.message}</p>
      <button onClick={() => reset()}>Try again</button>
    </div>
  );
}
```

This catches rendering errors inside that route segment.

---

## 12. Not Found Pages (not-found.tsx)

Custom 404 per route:

```
app/not-found.tsx
```

```tsx
export default function NotFound() {
  return <h1>Page Not Found</h1>;
}
```

Triggered via:

```tsx
import { notFound } from "next/navigation";

notFound();
```

---

## 13. Route Groups

Route groups organize routes without affecting URL.

Folder naming:

```
(groupName)
```

Example:

```
app/
  (auth)/
    login/
      page.tsx
    register/
      page.tsx
```

URLs remain:

```
/login
/register
```

But grouping helps organization.

---

## 14. Route Resolution Order

Next.js prioritizes:

1. Static routes
2. Dynamic routes
3. Catch-all routes

It builds a route tree and matches incoming requests accordingly.

---

## 15. Internal Rendering Flow

When visiting `/blog/first-post`:

1. Server matches route.
2. Root layout loads.
3. Blog layout loads (if exists).
4. Dynamic page loads.
5. Server component fetch executes.
6. HTML streamed.
7. Client components hydrate.

This layered execution is central to App Router design.

---

# Common Routing Mistakes

Using client hooks in server components
Forgetting `"use client"` in interactive components
Misplacing `page.tsx`
Confusing route groups with URL segments
Using dynamic routes when static suffices

---

# Architectural Insight

The App Router treats routing as a component tree rather than a path mapping table.

It is deeply integrated with:

* React Server Components
* Suspense
* Streaming
* Layout persistence

Routing is not an afterthought — it is foundational to Next.js architecture.

---

# Understanding Check – Module 3

1. Explain how file-based routing eliminates manual route configuration.
2. What is the difference between layout.tsx and page.tsx?
3. How do dynamic routes pass parameters to components?
4. When would you use a catch-all route?
5. Why must useRouter only be used inside client components?
6. How does loading.tsx relate to React Suspense?
7. What problem do route groups solve?

---


