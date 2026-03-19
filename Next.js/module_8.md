



---

# 📘 MODULE 8: Server Actions – Modern Full-Stack Mutation Pattern


# **Server Actions**

This feature fundamentally changes how full-stack React applications are written.

Historically, mutations required:

Client → API Route → Server → Database → Response → Re-fetch → UI Update

With Server Actions, the architecture becomes:

Client → Server Function → Database → Automatic Revalidation → UI Update

No explicit REST endpoint required.

This module will be detailed and rigorous.

---

# 1. What Are Server Actions?

A Server Action is a server-side function that can be directly invoked from a React component (usually through forms or event handlers).

It runs on the server but is referenced inside the React component tree.

Server Actions:

* Execute only on the server.
* Have access to databases and environment variables.
* Automatically integrate with revalidation.
* Eliminate the need for separate API routes for many use cases.

They are built on React’s experimental server function model and integrated deeply into the App Router.

---

# 2. Why Server Actions Exist

Traditional mutation flow in Next.js:

1. Create API route.
2. Write POST handler.
3. Call it from client using fetch().
4. Update UI manually.
5. Revalidate data manually.

This leads to:

* Boilerplate code
* Duplicate validation
* Over-fetching
* Complex state management

Server Actions simplify this pattern.

---

# 3. Declaring a Server Action

To declare a Server Action, you must add:

```tsx
"use server";
```

Example:

```tsx id="c1zqtx"
"use server";

export async function createUser(formData: FormData) {
  const name = formData.get("name");
  const email = formData.get("email");

  // Simulate DB operation
  console.log(name, email);
}
```

This function:

* Executes only on server.
* Is not included in client bundle.
* Cannot use client-side APIs.

---

# 4. Using Server Actions in Forms

Server Actions integrate directly with HTML forms.

Example:

```tsx id="a3yp64"
import { createUser } from "./actions";

export default function Page() {
  return (
    <form action={createUser}>
      <input name="name" placeholder="Name" />
      <input name="email" placeholder="Email" />
      <button type="submit">Submit</button>
    </form>
  );
}
```

When the form submits:

* The browser sends POST request.
* Next.js invokes createUser on server.
* No explicit API route required.

This is zero-boilerplate mutation handling.

---

# 5. Server Actions with Direct Database Access

Example using MySQL:

```tsx id="z6snbg"
"use server";

import mysql from "mysql2/promise";

export async function createUser(formData: FormData) {
  const name = formData.get("name");

  const connection = await mysql.createConnection({
    host: "localhost",
    user: "root",
    password: "password",
    database: "test",
  });

  await connection.execute(
    "INSERT INTO users (name) VALUES (?)",
    [name]
  );
}
```

No API route. No fetch. Direct DB mutation.

---

# 6. Returning Data from Server Actions

Server Actions can return values.

Example:

```tsx id="6v5oqc"
"use server";

export async function add(a: number, b: number) {
  return a + b;
}
```

Used inside client component:

```tsx id="phogz6"
"use client";

import { add } from "./actions";

export default function Calculator() {
  async function handleClick() {
    const result = await add(5, 3);
    alert(result);
  }

  return <button onClick={handleClick}>Add</button>;
}
```

Next.js handles serialization automatically.

---

# 7. Revalidation After Mutation

When data changes, static pages must be refreshed.

Next.js provides:

```tsx
import { revalidatePath } from "next/cache";
```

Example:

```tsx id="cz4vqu"
"use server";

import { revalidatePath } from "next/cache";

export async function createPost(formData: FormData) {
  const title = formData.get("title");

  await db.insert({ title });

  revalidatePath("/blog");
}
```

This ensures `/blog` updates immediately.

---

# 8. Tag-Based Revalidation

More scalable than path-based.

When fetching:

```tsx id="nwwd7z"
await fetch("/api/posts", {
  next: { tags: ["posts"] }
});
```

After mutation:

```tsx id="v2g3ys"
import { revalidateTag } from "next/cache";

revalidateTag("posts");
```

All pages using that tag refresh.

This is ideal for large systems.

---

# 9. Optimistic UI Updates

Optimistic UI means updating UI before server confirms mutation.

Example:

```tsx id="74iz6b"
"use client";

import { useOptimistic } from "react";
import { createPost } from "./actions";

export default function PostForm({ posts }) {
  const [optimisticPosts, addOptimisticPost] =
    useOptimistic(posts, (state, newPost) => [
      ...state,
      newPost
    ]);

  async function handleSubmit(formData: FormData) {
    const title = formData.get("title");
    addOptimisticPost({ title });
    await createPost(formData);
  }

  return (
    <form action={handleSubmit}>
      <input name="title" />
      <button type="submit">Add</button>

      {optimisticPosts.map((post, i) => (
        <p key={i}>{post.title}</p>
      ))}
    </form>
  );
}
```

UI updates instantly, improving UX.

---

# 10. Server Action Security Considerations

Server Actions:

* Run on server.
* Cannot be accessed directly via URL.
* Are serialized internally.

However:

You must still validate input.

Example:

```tsx id="t5c9eq"
if (!email || typeof email !== "string") {
  throw new Error("Invalid email");
}
```

Never trust form input blindly.

---

# 11. Limitations of Server Actions

* Cannot be used outside App Router.
* Must be serializable.
* Cannot return non-serializable objects.
* Not suitable for heavy streaming responses.

For complex REST APIs, route handlers may still be better.

---

# 12. When to Use Server Actions vs API Routes

Use Server Actions when:

* Mutation is directly tied to UI.
* Simple form submission.
* You want minimal boilerplate.
* You need automatic revalidation.

Use API routes when:

* Third-party services consume API.
* Mobile apps use your backend.
* Complex REST architecture needed.
* Public endpoints required.

---

# 13. Architectural Comparison

Traditional REST:

Client → fetch → API → DB → JSON → Client state update

Server Action:

Client → Action → DB → Revalidate → Server re-renders

Server Actions integrate deeply with React rendering lifecycle.

---

# 14. Internal Execution Model

When a Server Action is triggered:

1. Client submits action.
2. Framework serializes call.
3. Server executes function.
4. React re-renders affected segments.
5. HTML streamed.
6. UI updates.

This integrates mutation and rendering tightly.

---

# 15. Real-World Usage Pattern

In dashboard apps:

* Use Server Actions for create/update/delete.
* Use tag-based revalidation.
* Use optimistic UI for responsiveness.
* Keep data fetching in Server Components.

This yields a clean, maintainable architecture.

---

# Common Mistakes

Calling Server Action without `"use server"`
Not revalidating after mutation
Using Server Actions for public APIs
Returning complex objects
Skipping validation

---

# Understanding Check – Module 8

1. How do Server Actions eliminate the need for API routes?
2. Why must Server Actions include `"use server"`?
3. When should you use tag-based revalidation instead of path-based?
4. What is optimistic UI and why is it beneficial?
5. What are security considerations when using Server Actions?
6. When would API routes still be preferable?
7. Explain the architectural difference between REST mutation and Server Action mutation.

---

