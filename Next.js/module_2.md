

---

# 📘 MODULE 2: Installation & Project Setup

---

## 1. Node.js Requirements and Runtime Environment

Next.js is built on top of Node.js. This means your system must have a compatible Node runtime.

Next.js currently requires:

* Node.js 18.17 or later (LTS recommended)

You can verify your version:

```bash
node -v
```

If outdated, install from:
[https://nodejs.org](https://nodejs.org)

### Why Node.js is Required

Next.js uses Node for:

* Running the development server
* Executing server components
* Handling API routes
* Building production bundles
* Running server-side rendering

Without Node.js, Next.js cannot operate.

---

## 2. Creating a Next.js Project

Next.js provides an official CLI tool:

```bash
npx create-next-app@latest
```

This command:

* Downloads the latest Next.js
* Sets up folder structure
* Installs dependencies
* Configures TypeScript optionally
* Creates basic routing structure

---

### Full Example:

```bash
npx create-next-app@latest my-next-app
```

During setup, you will be asked:

* Use TypeScript? (Yes recommended)
* Use ESLint? (Yes)
* Use Tailwind CSS? (Optional but recommended)
* Use src directory? (Optional preference)
* Use App Router? (Yes — modern architecture)
* Customize import alias? (Optional)

Each choice modifies the project structure.

---

## 3. Generated Project Structure Explained

After installation, you will see something like:

```text
my-next-app/
│
├── app/
│   ├── layout.tsx
│   ├── page.tsx
│   ├── globals.css
│
├── public/
├── node_modules/
├── package.json
├── next.config.js
├── tsconfig.json
├── .eslintrc.json
```

Now we analyze each major component.

---

### 3.1 app/ Directory

This is the core routing layer.

Every folder represents a route segment.

`page.tsx` defines a route endpoint.

`layout.tsx` defines persistent UI structure.

---

### 3.2 public/ Directory

Contains static assets.

Example:

```text
public/logo.png
```

Accessible via:

```
/logo.png
```

Important: Files here are served directly without processing.

---

### 3.3 package.json

This defines project metadata and scripts.

Example:

```json
{
  "name": "my-next-app",
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint"
  }
}
```

These scripts define how your application runs.

---

## 4. Development Server

To start the application:

```bash
npm run dev
```

or

```bash
yarn dev
```

This runs:

```bash
next dev
```

The development server provides:

* Hot module replacement
* Fast refresh
* Error overlays
* File system watching

Default URL:

```
http://localhost:3000
```

---

## 5. Build Process and Production Mode

Production build:

```bash
npm run build
```

This executes:

```bash
next build
```

Internally, this:

* Compiles TypeScript
* Bundles JavaScript
* Performs tree shaking
* Generates static pages
* Analyzes routes
* Optimizes images
* Prepares production assets

After build:

```bash
npm start
```

Runs:

```bash
next start
```

This runs the optimized production server.

---

## 6. TypeScript vs JavaScript Setup

If you select TypeScript:

You get:

* `tsconfig.json`
* `.tsx` files
* Static type checking
* IDE autocompletion
* Type-safe props

Example Component:

```typescript
type Props = {
  title: string;
};

export default function Page({ title }: Props) {
  return <h1>{title}</h1>;
}
```

If using JavaScript:

```javascript
export default function Page({ title }) {
  return <h1>{title}</h1>;
}
```

TypeScript is strongly recommended for scalable systems.

Given your full-stack ambitions, TypeScript should be your default choice.

---

## 7. ESLint Integration

Next.js ships with built-in ESLint support.

Run:

```bash
npm run lint
```

This ensures:

* Code quality
* React best practices
* Accessibility checks
* Performance rules

Example lint rule:

```json
"react-hooks/exhaustive-deps": "warn"
```

---

## 8. Environment Variables

Next.js supports environment variables via:

```
.env.local
```

Example:

```env
DATABASE_URL=mysql://root:password@localhost:3306/mydb
JWT_SECRET=mysecretkey
```

To use in server components:

```javascript
process.env.DATABASE_URL
```

Important Rule:

Only variables prefixed with `NEXT_PUBLIC_` are accessible in client components.

Example:

```env
NEXT_PUBLIC_API_URL=https://api.example.com
```

Client usage:

```javascript
process.env.NEXT_PUBLIC_API_URL
```

This separation ensures sensitive data remains server-side.

---

## 9. next.config.js

This file customizes framework behavior.

Basic example:

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  images: {
    domains: ["example.com"]
  }
};

module.exports = nextConfig;
```

Common configuration areas:

* Image optimization
* Rewrites
* Redirects
* Webpack customization
* Experimental features

---

## 10. Strict Mode

React Strict Mode:

```javascript
reactStrictMode: true
```

This enables:

* Extra development checks
* Warning detection
* Double-invocation detection for unsafe side effects

Only active in development.

---

## 11. Using a src Directory (Optional)

If chosen:

```text
src/
  app/
  components/
```

This improves project organization in large systems.

---

## 12. Import Aliases

You may see:

```json
{
  "compilerOptions": {
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

This allows:

```javascript
import Button from "@/components/Button";
```

Instead of relative imports:

```javascript
import Button from "../../components/Button";
```

Improves maintainability.

---

## 13. Understanding Development vs Production Behavior

Development:

* Slower
* Debug-friendly
* No heavy optimizations
* Detailed error overlays

Production:

* Minified bundles
* Optimized builds
* Static pre-rendering
* Efficient caching

---

## 14. Turbopack (Modern Bundler)

Newer Next.js versions use Turbopack in development.

It is:

* Written in Rust
* Faster than Webpack
* Incremental bundler

Enabled automatically in newer versions.

---

## 15. Installing Additional Packages

Standard installation:

```bash
npm install axios mysql2 prisma
```

Next.js does not restrict normal Node package usage.

---

# Practical Example: Minimal Setup with Database

After installation:

Create `.env.local`:

```env
DATABASE_URL=mysql://root:password@localhost:3306/test
```

Create:

```
app/api/test/route.js
```

```javascript
export async function GET() {
  return Response.json({ message: "API working" });
}
```

Visit:

```
http://localhost:3000/api/test
```

You now have backend capability inside frontend framework.

---

# Common Setup Mistakes

Using client-side environment variables incorrectly
Forgetting to restart server after changing `.env`
Not using TypeScript in scalable apps
Misunderstanding difference between dev and build

---

# Understanding Check – Module 2

1. Why must Node.js be installed for Next.js to function?
2. What happens internally when you run `next build`?
3. Why are environment variables prefixed with `NEXT_PUBLIC_`?
4. What is the purpose of `next.config.js`?
5. What is the difference between `next dev` and `next start`?
6. Why is TypeScript advantageous in large-scale Next.js applications?

---


