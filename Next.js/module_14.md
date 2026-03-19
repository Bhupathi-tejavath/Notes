

---

# 📘 MODULE 14: Static Generation & Incremental Static Regeneration


# **Static Generation & Incremental Static Regeneration (ISR)**

This module is critical because it determines:

* Scalability
* Server load
* CDN efficiency
* SEO performance
* Cost of infrastructure

If you understand this deeply, you can design systems that serve millions of users with minimal backend cost.

We will cover:

* Static Site Generation (SSG) theory
* Build-time rendering mechanics
* Dynamic routes with static generation
* ISR deep internals
* Revalidation strategies
* On-demand revalidation
* Trade-offs vs SSR
* Architectural patterns
---

# 1. What Is Static Site Generation (SSG)?

Static Site Generation means:

> The HTML for a page is generated at build time and reused for every request.

Instead of generating HTML per request, Next.js pre-renders it once during:

```bash
npm run build
```

Then the HTML is served directly from:

* CDN
* Edge cache
* Static asset storage

This is the fastest possible rendering strategy.

---

# 2. How Static Generation Works Internally

During `next build`:

1. Next.js scans all routes.
2. Determines which routes are static.
3. Executes Server Components at build time.
4. Generates HTML output.
5. Stores static assets in `.next` build output.
6. Deploys to CDN.

When user requests page:

* No server computation required.
* CDN serves pre-generated HTML instantly.

This dramatically reduces:

* Server load
* Latency
* Infrastructure cost

---

# 3. Basic Static Page Example

```tsx
export default async function Page() {
  const data = await fetch("https://api.example.com/posts");
  const posts = await data.json();

  return (
    <div>
      {posts.map(post => (
        <p key={post.id}>{post.title}</p>
      ))}
    </div>
  );
}
```

If no dynamic signals exist, Next.js treats this as static.

Important:

Default fetch behavior in App Router is cache-enabled, meaning static generation unless overridden.

---

# 4. Dynamic Routes with Static Generation

Example:

```plaintext
app/blog/[slug]/page.tsx
```

To statically generate multiple dynamic routes, use:

```tsx
export async function generateStaticParams() {
  const posts = await fetch("https://api.example.com/posts")
    .then(res => res.json());

  return posts.map(post => ({
    slug: post.slug,
  }));
}
```

This tells Next.js:

Pre-generate these slugs at build time.

During build:

* Each slug generates separate HTML file.

---

# 5. Limitations of Pure Static Generation

Problem:

If data changes after deployment, static HTML becomes stale.

Example:

Blog post updated → static page does not reflect change.

Without ISR, you must:

* Rebuild entire application.
* Redeploy.

This is inefficient for frequently updated content.

---

# 6. Incremental Static Regeneration (ISR)

ISR allows static pages to be regenerated after deployment.

Instead of regenerating entire site, only specific pages update.

Example:

```tsx
export const revalidate = 60;
```

This means:

* Generate page at build time.
* After 60 seconds, Next.js regenerates in background.

Flow:

1. User visits page after 60s.
2. Old static page served immediately.
3. Background regeneration triggered.
4. Cache updated for future users.

This combines:

Static speed + Dynamic freshness.

---

# 7. Per-Fetch Revalidation

Instead of route-level revalidate, you can set fetch-level:

```tsx
await fetch("https://api.example.com/posts", {
  next: { revalidate: 60 }
});
```

This revalidates only that data source.

Better granularity.

---

# 8. On-Demand Revalidation

Sometimes you want immediate update after mutation.

Example in Server Action:

```tsx
"use server";

import { revalidatePath } from "next/cache";

export async function createPost() {
  await db.insert({ title: "New Post" });

  revalidatePath("/blog");
}
```

This invalidates specific path immediately.

---

# 9. Tag-Based Revalidation (Advanced)

More scalable for large apps.

During fetch:

```tsx
await fetch("/api/posts", {
  next: { tags: ["posts"] }
});
```

After mutation:

```tsx
import { revalidateTag } from "next/cache";

revalidateTag("posts");
```

All routes using that tag refresh automatically.

This is ideal for:

* E-commerce catalogs
* Dashboard stats
* Content management systems

---

# 10. Static vs ISR vs SSR Comparison

Static (SSG):

* Fastest.
* No server runtime.
* Data fixed until rebuild.

ISR:

* Fast.
* Regenerates incrementally.
* Balanced solution.

SSR (Dynamic):

* Fresh per request.
* Higher server cost.
* Needed for user-specific content.

Architectural principle:

> Use static wherever possible.
> Use ISR for periodically changing content.
> Use SSR only when personalization or real-time data required.

---

# 11. Forcing Static or Dynamic Rendering

Force static:

```tsx
export const dynamic = "force-static";
```

Force dynamic:

```tsx
export const dynamic = "force-dynamic";
```

Use carefully.

Overusing force-dynamic increases cost.

---

# 12. Caching Layers in ISR

ISR relies on:

* Build-time HTML
* CDN cache
* Revalidation queue
* Background regeneration

When regeneration occurs:

* First user after expiry gets stale page.
* Regeneration happens asynchronously.
* Future users receive fresh page.

This prevents blocking requests.

---

# 13. Handling Large Dynamic Datasets

For thousands of dynamic routes:

Do not pre-generate all at build time.

Instead:

Use fallback-style behavior:

In App Router, routes not returned in generateStaticParams:

* Are generated on first request.
* Then cached.

This enables scalable static generation.

---

# 14. Handling Personalized Content

Static generation cannot handle per-user personalization.

Example:

Dashboard with user-specific data.

Use:

```tsx
cache: "no-store"
```

or

```tsx
export const dynamic = "force-dynamic";
```

Never use static rendering for authenticated user content.

---

# 15. E-Commerce Architecture Example

Product page:

* Static generation.
* Revalidate every 300 seconds.
* Tag-based invalidation when product updated.

Admin dashboard:

* Dynamic rendering.
* No caching.
* Personalized.

Blog:

* Static generation.
* Revalidate daily.
* On-demand revalidate after edit.

This is real-world architecture.

---

# 16. Cost Efficiency Perspective

Static:

* Zero compute cost per request.
* CDN handles traffic.

ISR:

* Minimal compute.
* Occasional regeneration.

SSR:

* Compute per request.
* More server instances needed.

For large traffic apps, SSG + ISR drastically reduces hosting costs.

---

# 17. Common Pitfalls

Using force-dynamic unnecessarily
Using no-store globally
Pre-generating too many pages
Forgetting revalidate after mutation
Not understanding stale-while-revalidate behavior

---

# 18. Rendering Decision Tree

Ask:

Is content personalized?
→ Yes → SSR

Is content frequently changing?
→ Yes → ISR

Is content rarely changing?
→ Yes → SSG

Default should lean toward static.

---

# Understanding Check – Module 14

1. What happens during next build for static routes?
2. Why is static generation extremely scalable?
3. What problem does ISR solve?
4. What happens when revalidation time expires?
5. Why should dashboard pages not be statically generated?
6. What is the advantage of tag-based revalidation?
7. Compare infrastructure cost between SSG, ISR, and SSR.

---


