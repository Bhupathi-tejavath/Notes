
---

# 📘 MODULE 1: Introduction to Next.js

---

## 1. What is Next.js?

Next.js is a full-stack React framework that enables hybrid rendering, server-side execution, file-based routing, API handling, and production optimizations out of the box. It is built on top of React but extends React into a complete application framework capable of handling frontend rendering, backend logic, and build-time optimizations in a unified architecture.

Unlike plain React, which is primarily a UI library responsible for rendering components in the browser, Next.js introduces:

* A structured application architecture
* Built-in routing
* Server-side rendering
* Static site generation
* Incremental regeneration
* Edge execution
* API routes
* Automatic performance optimization

In essence, React handles “how UI updates,” while Next.js handles “how applications are structured, rendered, and deployed.”

From a systems perspective, Next.js is a hybrid rendering framework that supports multiple rendering strategies within the same application.

---

## 2. Why Next.js Over Plain React?

To understand this properly, we must examine the limitations of a standard React Single Page Application (SPA).

When you create a React app using Create React App or Vite:

* The browser downloads a large JavaScript bundle.
* React executes entirely in the client.
* The initial HTML file is mostly empty.
* SEO suffers because crawlers may not execute JS fully.
* Performance is slower on initial load.
* Routing requires third-party libraries (React Router).
* No built-in backend layer exists.

Next.js solves these issues by:

* Rendering HTML on the server before sending it to the browser.
* Pre-generating pages at build time.
* Splitting code automatically.
* Providing file-based routing.
* Allowing backend API creation in the same project.

Example comparison:

React SPA flow:

Browser → Downloads JS → React renders → User sees content

Next.js SSR flow:

Browser → Server renders HTML → Browser receives ready HTML → Hydration happens → Interactive UI

The difference is architectural, not just syntactic.

---

## 3. Rendering Paradigms Overview

Rendering refers to where and when HTML is generated.

### Client-Side Rendering (CSR)

CSR means rendering happens entirely in the browser after JavaScript loads.

Example (pure React behavior):

```javascript
useEffect(() => {
  fetch("/api/data")
    .then(res => res.json())
    .then(data => setData(data));
}, []);
```

The HTML is empty initially. Data loads after JS runs.

CSR characteristics:

* Slower first load
* Faster navigation after initial load
* Poor SEO unless pre-rendered

---

### Server-Side Rendering (SSR)

SSR means the server generates HTML on every request.

Example in Next.js (App Router):

```javascript
export default async function Page() {
  const res = await fetch("https://api.example.com/data", { cache: "no-store" });
  const data = await res.json();

  return <div>{data.title}</div>;
}
```

The server runs this function and sends ready HTML.

Characteristics:

* SEO-friendly
* Fresh data on each request
* Slightly higher server load

---

### Static Site Generation (SSG)

SSG generates HTML at build time.

```javascript
export const revalidate = 3600;

export default async function Page() {
  const res = await fetch("https://api.example.com/data");
  const data = await res.json();

  return <div>{data.title}</div>;
}
```

The page is generated once during build and reused.

Characteristics:

* Extremely fast
* Cached
* Ideal for blogs, marketing pages

---

### Incremental Static Regeneration (ISR)

ISR allows static pages to update after deployment.

```javascript
export const revalidate = 60;
```

This tells Next.js:

Regenerate this page in the background every 60 seconds.

ISR combines SSG speed with SSR freshness.

---

## 4. App Router vs Pages Router

Next.js historically used the Pages Router (`pages/` directory). Modern Next.js uses the App Router (`app/` directory).

### Pages Router

* Uses `getServerSideProps`
* Uses `getStaticProps`
* Uses `getStaticPaths`

Example:

```javascript
export async function getServerSideProps() {
  return { props: { data: "Hello" } };
}
```

### App Router (Modern)

* Uses Server Components
* Uses async components
* Uses built-in fetch
* Supports streaming
* Uses layout.js

Example:

```javascript
export default async function Page() {
  return <h1>Hello</h1>;
}
```

The App Router is more aligned with React’s future architecture.

---

## 5. Project Structure Overview

A basic Next.js App Router structure:

```
my-app/
│
├── app/
│   ├── layout.js
│   ├── page.js
│   ├── about/
│   │   └── page.js
│
├── public/
├── styles/
├── next.config.js
├── package.json
```

The `app` folder defines routes.

Each folder inside `app` becomes a URL segment.

---

## 6. How Next.js Works Internally

At build time:

* It analyzes the folder structure.
* Determines static vs dynamic routes.
* Pre-generates pages when possible.
* Creates optimized JS bundles.
* Performs tree shaking.

At runtime:

* Server components run on server.
* Client components hydrate in browser.
* React reconciles DOM efficiently.
* Streaming sends partial UI.

Next.js is tightly integrated with Webpack (or Turbopack in modern builds).

---

## 7. Hydration Explained

Hydration is the process where React attaches event listeners to server-rendered HTML.

Flow:

1. Server sends HTML.
2. Browser displays static content.
3. React JS loads.
4. React attaches interactivity.

Without hydration, server-rendered pages would be static.

---

# Practical Example: Minimal App Router Page

Create a file:

app/page.js

```javascript
export default function Home() {
  return (
    <main>
      <h1>Welcome to Next.js</h1>
    </main>
  );
}
```

Visit `/` → This renders server-side automatically.

---

# Architectural Insight

Next.js is not just “React with routing.”

It is:

A unified execution model combining:

* Server computation
* Client interactivity
* Static optimization
* Dynamic rendering
* Edge deployment

It bridges frontend and backend in a single runtime.

---

# Common Beginner Mistakes

Using `"use client"` everywhere unnecessarily
Using SSR for static data
Misunderstanding caching behavior
Mixing Pages Router and App Router without clarity

---

# Understanding Check – Module 1

1. Explain the architectural difference between React SPA and Next.js SSR in terms of request-response lifecycle.
2. Why is hydration required in server-side rendering?
3. When should you prefer SSG over SSR?
4. What problem does ISR solve that SSG alone cannot?
5. Why is App Router considered more future-aligned with React?

---


