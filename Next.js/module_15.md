
---

# 📘 MODULE 15: Deployment & Production Architecture


This module will cover:

* Production build mechanics
* Vercel deployment model
* Self-hosted deployment
* Dockerizing Next.js
* Environment management in production
* CI/CD integration
* Scaling strategies
* Edge vs Node runtime in production
* Monitoring & observability
---

# 1. Understanding the Production Build Process

When you run:

```bash
npm run build
```

Internally, Next.js performs:

1. Type checking (if TypeScript enabled)
2. Static analysis of routes
3. Server/Client component separation
4. Bundle splitting
5. Tree shaking
6. CSS optimization
7. Static page pre-rendering
8. Route manifest generation
9. Image optimization setup

Output directory:

```plaintext
.next/
```

Inside `.next`:

* Compiled server code
* Static assets
* Route definitions
* Pre-rendered HTML
* Client bundles

This build artifact is what gets deployed.

Important principle:

> Always test production using `npm run start`.

Development mode (`next dev`) does not reflect production behavior.

---

# 2. Production Server (Node Runtime)

When running:

```bash
npm run start
```

This executes:

```bash
next start
```

This launches a production Node server that:

* Serves pre-rendered pages
* Handles dynamic SSR
* Executes route handlers
* Runs server actions

This is suitable for:

* VPS hosting
* Docker containers
* Self-managed servers

---

# 3. Deployment on Vercel

Vercel is the native hosting platform for Next.js.

Deployment steps:

1. Push project to GitHub.
2. Connect repository to Vercel.
3. Vercel auto-detects Next.js.
4. Build runs in cloud.
5. Static assets deployed to CDN.
6. Serverless functions created for dynamic routes.

Architecture on Vercel:

* Static pages → CDN edge
* ISR pages → CDN with background regeneration
* Route handlers → Serverless functions
* Middleware → Edge runtime
* Server Actions → Serverless execution

Advantages:

* Zero configuration
* Automatic scaling
* Edge-first architecture
* Global CDN
* Automatic HTTPS

---

# 4. Serverless Execution Model

On Vercel:

* Each dynamic route runs as a serverless function.
* Functions scale automatically.
* Cold start possible.
* Stateless execution model.

Important:

Avoid relying on in-memory state.

Instead use:

* Database
* Redis
* External storage

---

# 5. Self-Hosting Next.js

If not using Vercel, you can deploy to:

* DigitalOcean
* AWS EC2
* Azure
* VPS servers

Steps:

```bash
npm run build
npm run start
```

Behind reverse proxy (Nginx or Apache).

Example Nginx config:

```nginx
server {
  listen 80;
  server_name example.com;

  location / {
    proxy_pass http://localhost:3000;
    proxy_http_version 1.1;
  }
}
```

Self-hosting gives:

* Full control
* Custom scaling
* Custom infra
* More responsibility

---

# 6. Dockerizing Next.js

Docker ensures consistent production environments.

Example `Dockerfile`:

```dockerfile
# Use Node base image
FROM node:18-alpine

WORKDIR /app

COPY package.json package-lock.json ./
RUN npm install

COPY . .

RUN npm run build

EXPOSE 3000

CMD ["npm", "start"]
```

Build and run:

```bash
docker build -t next-app .
docker run -p 3000:3000 next-app
```

Advantages:

* Portable deployment
* Environment isolation
* Easy scaling via Kubernetes

---

# 7. Environment Variables in Production

Production environments require secure configuration.

On Vercel:

* Add environment variables via dashboard.

On self-hosting:

* Use `.env.production`
* Or inject via server environment.

Example:

```env
DATABASE_URL=...
JWT_SECRET=...
```

Important:

Never expose secrets via `NEXT_PUBLIC_`.

Only prefix values intended for client.

---

# 8. Edge vs Node Runtime in Production

Node runtime:

* Supports full Node APIs.
* Database drivers allowed.
* Heavier cold starts.

Edge runtime:

* Faster global execution.
* Limited APIs.
* Ideal for middleware and lightweight logic.

Decision rule:

* Use Edge for request interception.
* Use Node for database-heavy operations.

---

# 9. Scaling Strategies

Scaling depends on rendering strategy.

Static pages:

* Infinite scalability via CDN.
* Zero compute cost per request.

ISR:

* Occasional compute.
* Scales well.

SSR:

* Compute per request.
* Requires autoscaling.

Serverless scaling:

* Automatically scales with demand.
* Stateless functions required.

---

# 10. Caching in Production

Caching layers:

1. Browser cache
2. CDN cache
3. ISR cache
4. Server-side cache

Use appropriate caching headers:

```tsx
return new Response(data, {
  headers: {
    "Cache-Control": "public, max-age=3600"
  }
});
```

Avoid disabling cache unnecessarily.

---

# 11. Monitoring & Logging

Production systems require observability.

Options:

* Vercel Analytics
* Google Analytics
* Datadog
* Sentry (error monitoring)
* Custom logging via Winston

Example Sentry setup:

```bash
npm install @sentry/nextjs
```

Use to monitor:

* Server errors
* Client errors
* Performance metrics

---

# 12. Handling Errors in Production

Use:

* error.tsx for UI boundaries
* try/catch in route handlers
* Centralized logging

Never expose stack traces in production.

Example:

```tsx
catch (error) {
  return new Response("Internal Server Error", { status: 500 });
}
```

---

# 13. CI/CD Integration

Typical pipeline:

1. Push code to GitHub.
2. CI runs tests.
3. Build executed.
4. Deployment triggered automatically.
5. Environment variables injected.
6. CDN updated.

With GitHub Actions:

```yaml
name: Deploy

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: npm install
      - run: npm run build
```

Automated deployment ensures consistency.

---

# 14. Blue-Green Deployment Concept

Instead of replacing current deployment:

* Deploy new version separately.
* Switch traffic when verified.

Prevents downtime.

Supported automatically on Vercel.

---

# 15. Handling Database in Production

Use:

* Managed databases (PlanetScale, AWS RDS)
* Connection pooling
* Prisma with pooling
* Avoid new DB connection per request

Serverless environments require careful connection management.

---

# 16. Production Security Checklist

* HTTPS enabled.
* HTTP-only cookies.
* Secure headers.
* Environment secrets protected.
* Rate limiting.
* Input validation.
* Avoid exposing internal APIs publicly.

---

# 17. Production Readiness Checklist

Before launch:

* Run `npm run build`
* Test in production mode.
* Analyze bundle size.
* Verify SEO metadata.
* Test authentication.
* Test error boundaries.
* Verify robots.txt and sitemap.
* Confirm caching strategy.

---

# 18. Infrastructure Architecture Example

Large production system:

CDN → Edge Middleware → Static Pages → Serverless Functions → Database

This layered architecture ensures:

* High scalability
* Low latency
* Cost efficiency
* Security

---

# Common Mistakes

Testing only in dev mode
Exposing secrets to client
Using dynamic rendering unnecessarily
Ignoring connection pooling
No monitoring setup
Ignoring caching headers

---

# Understanding Check – Module 15

1. What happens internally during `next build`?
2. Why is dev mode not representative of production performance?
3. How does serverless scaling work in Next.js?
4. Why should in-memory state not be relied upon in serverless?
5. When should you use Edge runtime vs Node runtime?
6. Why is connection pooling important in production?
7. What are advantages of Dockerizing Next.js?
8. Why is monitoring essential in production?

---

