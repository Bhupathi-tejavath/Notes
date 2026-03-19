

---

# 📘 MODULE 7: Template Engines (Server-Side Rendering Deep Dive)

We will cover:

1. What is Server-Side Rendering (SSR)?
2. Why template engines exist
3. How Express integrates template engines
4. Setting a view engine
5. EJS (deep dive)
6. Pug (overview)
7. Handlebars (overview)
8. Passing data to templates
9. Layouts & partials
10. Static files with SSR
11. Rendering conditionally
12. Loops in templates
13. Security considerations
14. MVC structure with templates
15. Common mistakes
16. Exercises

---

# 1️⃣ What is Server-Side Rendering (SSR)?

---

## Definition

Server-Side Rendering means:

> HTML is generated on the server and sent fully rendered to the client.

Instead of sending JSON:

```json
{
  "name": "Pradeep"
}
```

Server sends:

```html
<h1>Welcome Pradeep</h1>
```

---

## SSR Flow

```
Client Request
     ↓
Express Route
     ↓
Template Engine
     ↓
Rendered HTML
     ↓
Browser
```

---

## When SSR is Useful

* SEO-sensitive apps
* Faster first paint
* Simpler frontend
* Traditional web apps

---

# 2️⃣ Why Template Engines Exist

Without template engine:

```js
res.send(`
    <html>
        <body>
            <h1>${username}</h1>
        </body>
    </html>
`);
```

Problems:

* Hard to maintain
* Poor readability
* No structure
* No layout reuse

Template engines solve this by:

* Separating logic from markup
* Supporting loops
* Supporting conditionals
* Supporting layouts

---

# 3️⃣ How Express Integrates Template Engines

Express supports many engines via:

```js
app.set('view engine', 'ejs');
```

And:

```js
res.render('templateName', data);
```

---

# 4️⃣ Setting a View Engine (Step-by-Step)

---

## Step 1: Install Engine

Example using EJS:

```bash
npm install ejs
```

---

## Step 2: Configure Express

```js
const express = require('express');
const app = express();

app.set('view engine', 'ejs');
```

---

## Step 3: Create `views` Folder

```
project/
│
├── views/
│   └── home.ejs
```

---

## Step 4: Create Template

`home.ejs`

```html
<h1>Welcome <%= name %></h1>
```

---

## Step 5: Render Template

```js
app.get('/', (req, res) => {
    res.render('home', { name: 'Pradeep' });
});
```

---

# 5️⃣ EJS Deep Dive

EJS = Embedded JavaScript

---

## Syntax Types

| Syntax   | Purpose               |
| -------- | --------------------- |
| `<%= %>` | Output escaped value  |
| `<%- %>` | Output unescaped HTML |
| `<% %>`  | Logic (no output)     |

---

## Example: Variable Output

```html
<h1>Hello <%= user %></h1>
```

---

## Example: Loop

```html
<ul>
    <% users.forEach(user => { %>
        <li><%= user %></li>
    <% }) %>
</ul>
```

---

## Example: Conditional

```html
<% if (isAdmin) { %>
    <p>Admin Panel</p>
<% } else { %>
    <p>User Panel</p>
<% } %>
```

---

## Difference: `<%= %>` vs `<%- %>`

Escaped:

```html
<%= "<script>" %>
```

Outputs safe HTML.

Unescaped:

```html
<%- "<strong>Hello</strong>" %>
```

Renders actual HTML.

⚠ Avoid `<%- %>` with user input (XSS risk).

---

# 6️⃣ Pug Overview

Pug uses indentation-based syntax.

Install:

```bash
npm install pug
```

Example:

```pug
h1 Hello #{name}
```

More concise but less HTML-like.

---

# 7️⃣ Handlebars Overview

Install:

```bash
npm install express-handlebars
```

Example:

```handlebars
<h1>Hello {{name}}</h1>
```

More logic-less philosophy.

---

# 8️⃣ Passing Data to Templates

---

## Single Value

```js
res.render('home', { name: 'Pradeep' });
```

---

## Multiple Values

```js
res.render('dashboard', {
    name: 'Pradeep',
    age: 22,
    isAdmin: true
});
```

---

## Passing Arrays

```js
res.render('users', {
    users: ['A', 'B', 'C']
});
```

---

# 9️⃣ Layouts & Partials

---

## Problem

Header and footer repeated in every page.

---

## Solution: Layout

Install:

```bash
npm install express-ejs-layouts
```

Setup:

```js
const expressLayouts = require('express-ejs-layouts');
app.use(expressLayouts);
```

Create:

`views/layout.ejs`

```html
<html>
<head>
    <title>My App</title>
</head>
<body>
    <%- body %>
</body>
</html>
```

---

Now all pages inherit layout.

---

## Partials

Reusable components:

```
views/partials/header.ejs
views/partials/footer.ejs
```

Use in template:

```html
<%- include('partials/header') %>
```

---

# 🔟 Serving Static Files with SSR

Add:

```js
app.use(express.static('public'));
```

Folder:

```
public/
  css/
  js/
  images/
```

---

In template:

```html
<link rel="stylesheet" href="/css/style.css">
```

---

# 1️⃣1️⃣ MVC Pattern with Templates

---

## Structure

```
src/
├── routes/
├── controllers/
├── views/
```

---

## Route

```js
router.get('/', homeController);
```

---

## Controller

```js
function homeController(req, res) {
    res.render('home', { name: 'Pradeep' });
}
```

---

Separation improves maintainability.

---

# 1️⃣2️⃣ Security Considerations

---

⚠ Never trust user input.

If you do:

```html
<%- userInput %>
```

You risk XSS.

Always escape or sanitize.

---

# 1️⃣3️⃣ Performance Considerations

* SSR consumes CPU
* Heavy logic in templates is bad
* Keep templates clean
* Avoid database queries in templates

---

# 1️⃣4️⃣ Common Mistakes

---

❌ Forgetting `views` folder
❌ Wrong file extension
❌ Not installing engine
❌ Mixing heavy logic in templates
❌ Using unescaped output dangerously

---

# 🧪 Exercises

---

### Exercise 1

Create:

```plaintext
GET /
```

Render page with:

* Name
* List of products

---

### Exercise 2

Create layout with:

* Header
* Footer
* Dynamic page content

---

### Exercise 3

Create page:

```plaintext
GET /dashboard
```

If `isAdmin` true → show admin panel
Else → show user panel

---

# 🎯 After Module 7 You Must Master

* What SSR is
* How Express integrates template engines
* How `res.render()` works
* EJS syntax deeply
* Layout and partial usage
* Passing dynamic data
* MVC separation with templates
* XSS risk awareness

---


