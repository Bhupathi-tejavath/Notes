




---

# 📘 MODULE 9: Styling in Next.js


This module is not merely about writing CSS. It is about:

* Scoping strategies
* Performance implications
* Bundle optimization
* Image and font optimization
* CSS architecture decisions
* Production-ready styling patterns


---

# 1. Global CSS

Global CSS is applied across the entire application.

In the App Router, global styles are typically imported in the root layout:

```tsx
// app/layout.tsx
import "./globals.css";
```

Example `globals.css`:

```css
body {
  margin: 0;
  font-family: sans-serif;
  background-color: #f4f4f4;
}

h1 {
  color: #333;
}
```

Important Rules:

* Global CSS can only be imported inside `layout.tsx` or `page.tsx`.
* It cannot be imported inside arbitrary components (to prevent CSS conflicts).
* It affects the entire DOM.

Use global CSS for:

* Reset styles
* Base typography
* Theme variables
* Utility classes

Avoid placing component-specific styles here.

---

# 2. CSS Modules

CSS Modules provide locally scoped CSS.

File naming convention:

```plaintext
Component.module.css
```

Example:

```css
/* Button.module.css */
.button {
  background-color: blue;
  color: white;
  padding: 10px;
}
```

Usage:

```tsx
import styles from "./Button.module.css";

export default function Button() {
  return <button className={styles.button}>Click</button>;
}
```

Behind the scenes:

Next.js transforms the class name into a unique identifier.

Example output:

```css
.button__3h92k
```

This prevents:

* Naming collisions
* Global leakage
* CSS override bugs

CSS Modules are ideal for:

* Component-based styling
* Medium-scale projects
* Scoped UI components

---

# 3. Sass Integration

Next.js supports Sass natively.

Install:

```bash
npm install sass
```

Create:

```plaintext
styles.module.scss
```

Example:

```scss
$primary-color: #0070f3;

.button {
  background-color: $primary-color;

  &:hover {
    background-color: darken($primary-color, 10%);
  }
}
```

Sass allows:

* Variables
* Nesting
* Mixins
* Partial imports

Use Sass when:

* You prefer traditional CSS architecture
* Working on legacy style systems
* Managing large design systems

---

# 4. Tailwind CSS in Next.js

Tailwind integrates seamlessly during project setup.

If not installed:

```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

In `tailwind.config.js`:

```javascript
module.exports = {
  content: [
    "./app/**/*.{js,ts,jsx,tsx}",
    "./components/**/*.{js,ts,jsx,tsx}"
  ],
  theme: {
    extend: {},
  },
  plugins: [],
};
```

In `globals.css`:

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

Example usage:

```tsx
export default function Card() {
  return (
    <div className="bg-white shadow-lg p-6 rounded-xl">
      <h2 className="text-xl font-bold">Title</h2>
    </div>
  );
}
```

Tailwind advantages:

* Utility-first design
* No CSS file management
* Excellent tree-shaking
* Fast UI prototyping
* Reduced context switching

Given your UI experience, Tailwind is often optimal for scalable dashboards.

---

# 5. Styled Components (CSS-in-JS)

Install:

```bash
npm install styled-components
```

Example:

```tsx
"use client";

import styled from "styled-components";

const Button = styled.button`
  background-color: blue;
  color: white;
  padding: 10px;
`;

export default function Page() {
  return <Button>Click</Button>;
}
```

Important:

Styled Components require Client Components.

Pros:

* Dynamic styles
* Theming
* JS-powered styling

Cons:

* Increases bundle size
* Requires hydration
* Less efficient than server-rendered CSS

In performance-critical apps, Tailwind or CSS Modules are preferable.

---

# 6. Fonts Optimization (next/font)

Next.js optimizes fonts automatically.

Example:

```tsx
import { Inter } from "next/font/google";

const inter = Inter({ subsets: ["latin"] });

export default function Layout({ children }) {
  return (
    <body className={inter.className}>
      {children}
    </body>
  );
}
```

Benefits:

* Self-hosted fonts
* Automatic preloading
* No layout shift
* Reduced network latency

This eliminates Google Fonts external blocking calls.

---

# 7. Image Optimization (next/image)

Next.js provides an optimized image component.

Example:

```tsx
import Image from "next/image";

export default function Page() {
  return (
    <Image
      src="/profile.jpg"
      width={500}
      height={300}
      alt="Profile"
    />
  );
}
```

Benefits:

* Automatic resizing
* Lazy loading
* WebP conversion
* Responsive images
* Reduced bandwidth

For external domains:

Add in `next.config.js`:

```javascript
images: {
  domains: ["example.com"],
}
```

This ensures security and optimization.

---

# 8. Responsive Images

```tsx
<Image
  src="/hero.jpg"
  fill
  sizes="(max-width: 768px) 100vw, 50vw"
  alt="Hero"
/>
```

Next.js automatically generates multiple resolutions.

---

# 9. CSS Performance Considerations

Next.js automatically:

* Removes unused CSS
* Splits CSS per route
* Minimizes CSS in production

Best practice:

* Avoid large global CSS files
* Avoid client-only style systems unnecessarily
* Prefer static styling when possible

---

# 10. Theming Strategy

You can implement dark mode using Tailwind:

```html
<html class="dark">
```

Tailwind config:

```javascript
darkMode: "class",
```

Example:

```tsx
<div className="bg-white dark:bg-black">
```

Use server-side detection to set initial theme without flicker.

---

# 11. Layout Shift and Core Web Vitals

Fonts and images affect:

* CLS (Cumulative Layout Shift)
* LCP (Largest Contentful Paint)

Using:

* next/font
* next/image
* Fixed dimensions

Improves Core Web Vitals automatically.

---

# 12. When to Use Which Styling Strategy

Small apps → CSS Modules
Dashboards → Tailwind
Highly dynamic theming → Styled Components
Legacy CSS → Sass

Avoid mixing too many styling paradigms.

---

# 13. Production Styling Checklist

* Use next/font for fonts
* Use next/image for images
* Minimize global CSS
* Avoid large client-only styling libraries
* Enable compression
* Use CDN

---

# Common Mistakes

Using `<img>` instead of `next/image`
Importing global CSS inside components
Overusing CSS-in-JS in server components
Not configuring Tailwind content paths correctly

---

# Understanding Check – Module 9

1. Why must global CSS be imported only in layout or page files?
2. How do CSS Modules prevent naming collisions?
3. Why is next/font better than linking Google Fonts directly?
4. What performance benefits does next/image provide?
5. Why can Styled Components increase bundle size?
6. When should Tailwind be preferred over CSS Modules?
7. How does Next.js optimize CSS automatically?

---


