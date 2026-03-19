
---

# 📘 MODULE 13: SEO & Metadata Engineering


This module will cover:

* SEO fundamentals in SSR frameworks
* Metadata API deep dive
* Dynamic metadata generation
* Open Graph & Twitter Cards
* Canonical URLs
* robots.txt
* Sitemap generation
* Structured data (JSON-LD)
* Programmatic SEO patterns
* Technical SEO considerations

---

# 1. SEO Fundamentals in Next.js

Search engines primarily rely on:

* HTML content
* Meta tags
* Structured data
* Crawlability

In SPA (client-rendered React):

* Initial HTML is mostly empty.
* Search engines must execute JavaScript.
* SEO becomes inconsistent.

In Next.js:

* Server Components generate HTML on server.
* Static and SSR pages are crawlable.
* Metadata is generated server-side.

This makes Next.js inherently SEO-friendly.

However, correct metadata configuration is essential.

---

# 2. Metadata API (App Router)

In App Router, metadata is defined via:

```tsx id="meta1"
export const metadata = {
  title: "Page Title",
  description: "Page description",
};
```

This automatically injects:

```html id="meta2"
<title>Page Title</title>
<meta name="description" content="Page description">
```

The Metadata API replaces manual `<Head>` usage.

---

# 3. Full Metadata Structure

Example:

```tsx id="meta3"
export const metadata = {
  title: "Dashboard",
  description: "User dashboard page",
  keywords: ["dashboard", "nextjs", "admin"],
  authors: [{ name: "Pradeep" }],
  creator: "Pradeep",
  publisher: "MyCompany",
};
```

This generates corresponding meta tags.

Important:

Metadata is hierarchical and merges across layouts.

---

# 4. Dynamic Metadata

For dynamic routes (e.g., blog posts), metadata must reflect content dynamically.

Use:

```tsx id="meta4"
export async function generateMetadata({ params }) {
  const post = await fetch(
    `https://api.example.com/posts/${params.slug}`
  ).then(res => res.json());

  return {
    title: post.title,
    description: post.summary,
  };
}
```

This ensures:

* Each blog post has unique title.
* Search engines index pages correctly.
* Social previews are accurate.

---

# 5. Title Templates

To maintain consistent branding:

In root layout:

```tsx id="meta5"
export const metadata = {
  title: {
    template: "%s | MyApp",
    default: "MyApp",
  },
};
```

In page:

```tsx id="meta6"
export const metadata = {
  title: "Dashboard",
};
```

Result:

```
Dashboard | MyApp
```

This ensures scalable metadata consistency.

---

# 6. Open Graph (Social Media Optimization)

Open Graph controls link previews on:

* Facebook
* LinkedIn
* WhatsApp
* Slack

Example:

```tsx id="meta7"
export const metadata = {
  openGraph: {
    title: "Blog Post",
    description: "Deep dive article",
    url: "https://example.com/blog/post",
    siteName: "MySite",
    images: [
      {
        url: "https://example.com/og-image.jpg",
        width: 1200,
        height: 630,
      },
    ],
    type: "article",
  },
};
```

Without Open Graph, previews look generic.

---

# 7. Twitter Card Metadata

Example:

```tsx id="meta8"
export const metadata = {
  twitter: {
    card: "summary_large_image",
    title: "Blog Post",
    description: "Deep dive article",
    images: ["https://example.com/twitter-image.jpg"],
  },
};
```

This improves Twitter preview quality.

---

# 8. Canonical URLs

Canonical tags prevent duplicate content issues.

Example:

```tsx id="meta9"
export const metadata = {
  alternates: {
    canonical: "https://example.com/blog/post",
  },
};
```

Search engines will treat this as authoritative URL.

Important for:

* Pagination
* Filtered URLs
* Marketing parameters

---

# 9. robots.txt in Next.js

You can create:

```plaintext id="meta10"
app/robots.ts
```

Example:

```tsx id="meta11"
export default function robots() {
  return {
    rules: [
      {
        userAgent: "*",
        allow: "/",
        disallow: "/admin",
      },
    ],
    sitemap: "https://example.com/sitemap.xml",
  };
}
```

This automatically generates robots.txt.

---

# 10. Sitemap Generation

Create:

```plaintext id="meta12"
app/sitemap.ts
```

Example:

```tsx id="meta13"
export default async function sitemap() {
  const posts = await fetch("https://api.example.com/posts")
    .then(res => res.json());

  return posts.map(post => ({
    url: `https://example.com/blog/${post.slug}`,
    lastModified: post.updatedAt,
  }));
}
```

Search engines use sitemap to:

* Discover pages faster
* Crawl efficiently

---

# 11. Structured Data (JSON-LD)

Structured data helps search engines understand content semantically.

Example for blog post:

```tsx id="meta14"
export default function Page() {
  const structuredData = {
    "@context": "https://schema.org",
    "@type": "Article",
    headline: "Blog Post",
    author: {
      "@type": "Person",
      name: "Pradeep",
    },
  };

  return (
    <>
      <script
        type="application/ld+json"
        dangerouslySetInnerHTML={{
          __html: JSON.stringify(structuredData),
        }}
      />
      <h1>Blog Post</h1>
    </>
  );
}
```

This enhances:

* Rich snippets
* Knowledge panels
* Article indexing

---

# 12. Programmatic SEO Strategy

For large systems:

* Generate thousands of pages dynamically.
* Use dynamic metadata.
* Use sitemap generation.
* Use canonical URLs.
* Use structured data.

Example:

E-commerce platform:

* `/products/[slug]`
* Each product has dynamic metadata.
* Sitemap auto-generated.

This is programmatic SEO.

---

# 13. Technical SEO Considerations

Ensure:

* No broken links.
* No duplicate titles.
* No duplicate descriptions.
* Proper heading hierarchy (H1 → H2 → H3).
* Mobile responsiveness.
* Fast LCP.
* HTTPS enabled.

Next.js helps with:

* Pre-rendering.
* Fast page loads.
* Automatic code splitting.

---

# 14. Avoiding SEO Pitfalls

Do not:

* Use `no-store` unnecessarily for public content.
* Block search engine crawlers in middleware accidentally.
* Forget to add canonical URLs.
* Use client-side rendering for critical content.
* Hide important content behind client-only components.

---

# 15. SEO Execution Flow

When search engine visits:

1. Middleware executes.
2. Server renders page.
3. Metadata injected.
4. HTML returned.
5. Bot reads structured data.
6. Sitemap improves discovery.

Everything must work server-side.

---

# 16. SEO for Authenticated Pages

Protected routes (e.g., dashboard) should:

* Be disallowed in robots.txt.
* Not indexed.
* Redirect unauthenticated users.

Example:

```tsx id="meta15"
disallow: "/dashboard"
```

Public content should be indexable.

---

# 17. Core Web Vitals & SEO

Google ranking considers:

* Page speed
* Mobile performance
* CLS
* LCP
* Accessibility

Next.js optimizations directly influence SEO ranking.

Performance and SEO are interconnected.

---

# Common Mistakes

Using same metadata for all pages
Not generating dynamic metadata
Ignoring canonical URLs
Not generating sitemap
Blocking bots accidentally via middleware
Ignoring structured data

---

# Understanding Check – Module 13

1. Why is Next.js inherently more SEO-friendly than SPA?
2. What is the purpose of generateMetadata?
3. Why are Open Graph tags important?
4. What problem do canonical URLs solve?
5. Why is structured data important?
6. How does sitemap improve crawlability?
7. Why should dashboard routes be disallowed in robots.txt?

---


