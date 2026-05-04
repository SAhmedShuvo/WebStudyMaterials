# ⚛️ React Server Component: Server Component vs Client Component

Next.js 13+ এ দুই ধরনের component আছে:

| বিষয় | Server Component | Client Component |
|------|-----------------|-----------------|
| Render হয় | Server-এ | Browser-এ |
| JavaScript bundle | Client-এ যায় না | Client-এ যায় |
| State/Hook ব্যবহার | ❌ করা যায় না | ✅ করা যায় |
| Database access | ✅ সরাসরি করা যায় | ❌ করা যায় না |
| Default | ✅ হ্যাঁ | ❌ না (manually declare করতে হয়) |

---

## 🖥️ Server Component

Next.js-এ **default-ই সব component Server Component**।  
এখানে `async/await`, database call, API fetch সরাসরি করা যায়।  
কোনো `useState`, `useEffect` বা browser API ব্যবহার করা যায় না।

### উদাহরণ:

```tsx
// app/users/page.tsx
// কোনো "use client" নেই, তাই এটি Server Component

async function UsersPage() {
  const res = await fetch("https://jsonplaceholder.typicode.com/users");
  const users = await res.json();

  return (
    <ul>
      {users.map((user: { id: number; name: string }) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}

export default UsersPage;
```

> ✅ সরাসরি `fetch` করা হয়েছে — কোনো `useEffect` লাগেনি।  
> ✅ এই component-এর JavaScript browser-এ যায় না, শুধু HTML যায়।

---

## 💻 Client Component

যখন `useState`, `useEffect`, `onClick`, বা যেকোনো browser API দরকার হয়,  
তখন ফাইলের একদম উপরে **`"use client"`** লিখতে হয়।

### উদাহরণ:

```tsx
// app/components/Counter.tsx
"use client";

import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increase</button>
    </div>
  );
}

export default Counter;
```

> ✅ `"use client"` দিয়ে declare করা হয়েছে।  
> ✅ `useState` এবং `onClick` ব্যবহার করা যাচ্ছে।

---

## 🔀 একসাথে ব্যবহার (Mix করা)

Server Component-এর ভেতরে Client Component import করা যায়।  
কিন্তু **Client Component-এর ভেতরে Server Component import করা যায় না।**

```tsx
// app/page.tsx (Server Component)
import Counter from "./components/Counter"; // Client Component

export default function Home() {
  return (
    <main>
      <h1>Hello from Server</h1>
      <Counter /> {/* Client Component এখানে বসানো হয়েছে */}
    </main>
  );
}
```
# 🗂️ Next.js Routing — Folder-Based Routing সম্পূর্ণ গাইড

Next.js 13+ এ **App Router** ব্যবহার করে। এখানে routing হয় **folder structure** দিয়ে।  
আলাদা করে কোনো route config লিখতে হয় না।

> 📁 `app/` ফোল্ডারের ভেতরে যা folder বানাবে, সেটাই route হয়ে যাবে।

---

## 📌 ১. Basic Routing

প্রতিটি folder = একটি route  
প্রতিটি folder-এ একটি `page.tsx` ফাইল থাকতে হবে।

### File Structure:

```
app/
├── page.tsx           → /
├── about/
│   └── page.tsx       → /about
└── contact/
    └── page.tsx       → /contact
```

### উদাহরণ:

```tsx
// app/about/page.tsx
export default function AboutPage() {
  return <h1>আমাদের সম্পর্কে</h1>;
}
```

---

## 📌 ২. Nested Routing

Folder-এর ভেতরে Folder বানালেই nested route হয়।

### File Structure:

```
app/
└── dashboard/
    ├── page.tsx           → /dashboard
    ├── settings/
    │   └── page.tsx       → /dashboard/settings
    └── profile/
        └── page.tsx       → /dashboard/profile
```

### উদাহরণ:

```tsx
// app/dashboard/settings/page.tsx
export default function SettingsPage() {
  return <h1>Settings Page</h1>;
}
```

---

## 📌 ৩. Dynamic Routing — `[param]`

URL-এ variable অংশ থাকলে folder-এর নাম **`[paramName]`** দিয়ে বানাতে হয়।

### File Structure:

```
app/
└── products/
    ├── page.tsx              → /products
    └── [id]/
        └── page.tsx          → /products/1 বা /products/99
```

### উদাহরণ:

```tsx
// app/products/[id]/page.tsx
export default function ProductPage({ params }: { params: { id: string } }) {
  return <h1>Product ID: {params.id}</h1>;
}
```

> `/products/5` → `params.id = "5"`  
> `/products/abc` → `params.id = "abc"`

---

## 📌 ৪. Nested Dynamic Routing

Dynamic segment-এর ভেতরে আরো dynamic segment বানানো যায়।

### File Structure:

```
app/
└── blog/
    └── [categoryId]/
        └── [postId]/
            └── page.tsx     → /blog/tech/my-first-post
```

### উদাহরণ:

```tsx
// app/blog/[categoryId]/[postId]/page.tsx
export default function BlogPostPage({
  params,
}: {
  params: { categoryId: string; postId: string };
}) {
  return (
    <div>
      <h1>Category: {params.categoryId}</h1>
      <h2>Post: {params.postId}</h2>
    </div>
  );
}
```

---

## 📌 ৫. Catch-All Routing — `[...param]`

একটি route দিয়ে **যতগুলো segment ইচ্ছা** ধরতে চাইলে `[...param]` ব্যবহার করো।

### File Structure:

```
app/
└── docs/
    └── [...slug]/
        └── page.tsx
```

### উদাহরণ:

```tsx
// app/docs/[...slug]/page.tsx
export default function DocsPage({ params }: { params: { slug: string[] } }) {
  return <h1>Slug: {params.slug.join(" / ")}</h1>;
}
```

| URL | `params.slug` |
|-----|--------------|
| `/docs/intro` | `["intro"]` |
| `/docs/guide/setup` | `["guide", "setup"]` |
| `/docs/a/b/c` | `["a", "b", "c"]` |

> ⚠️ `/docs` → এই route কাজ করবে না, কারণ কোনো slug নেই।

---

## 📌 ৬. Optional Catch-All — `[[...param]]`

`[...param]` এর মতোই, কিন্তু **segment ছাড়াও** route match করে।

### File Structure:

```
app/
└── shop/
    └── [[...filters]]/
        └── page.tsx
```

### উদাহরণ:

```tsx
// app/shop/[[...filters]]/page.tsx
export default function ShopPage({
  params,
}: {
  params: { filters?: string[] };
}) {
  return (
    <h1>
      {params.filters ? `Filters: ${params.filters.join(", ")}` : "All Products"}
    </h1>
  );
}
```

| URL | `params.filters` |
|-----|-----------------|
| `/shop` | `undefined` |
| `/shop/shoes` | `["shoes"]` |
| `/shop/shoes/red` | `["shoes", "red"]` |

---

## 📌 ৭. Route Groups — `(groupName)`

Route-কে **group করার জন্য** parentheses দিয়ে folder বানানো যায়।  
এই folder-এর নাম URL-এ দেখা যায় না।

### File Structure:

```
app/
└── (auth)/
    ├── login/
    │   └── page.tsx     → /login
    └── register/
        └── page.tsx     → /register
```

> `(auth)` folder টি URL-এ আসে না, শুধু code organize করার জন্য।

### কাজে লাগে:
- আলাদা layout দেওয়ার জন্য (যেমন auth pages-এ আলাদা layout)
- Code কে logically ভাগ করার জন্য

```
app/
├── (marketing)/
│   ├── layout.tsx       ← শুধু marketing pages-এর layout
│   ├── page.tsx         → /
│   └── about/
│       └── page.tsx     → /about
└── (dashboard)/
    ├── layout.tsx       ← শুধু dashboard-এর layout
    └── dashboard/
        └── page.tsx     → /dashboard
```

---

## 📌 ৮. Parallel Routes — `@folder`

একই page-এ **একাধিক page একসাথে** render করার জন্য।

### File Structure:

```
app/
└── dashboard/
    ├── layout.tsx
    ├── page.tsx
    ├── @analytics/
    │   └── page.tsx
    └── @team/
        └── page.tsx
```

### layout.tsx এ ব্যবহার:

```tsx
// app/dashboard/layout.tsx
export default function DashboardLayout({
  children,
  analytics,
  team,
}: {
  children: React.ReactNode;
  analytics: React.ReactNode;
  team: React.ReactNode;
}) {
  return (
    <div>
      {children}
      <div style={{ display: "flex", gap: "16px" }}>
        {analytics}
        {team}
      </div>
    </div>
  );
}
```

> ✅ একই URL-এ dashboard, analytics আর team একসাথে দেখাবে।

---

## 📌 ৯. Intercepting Routes — `(.)` `(..)` `(...)`

অন্য route-কে **বর্তমান layout-এ আটকে** দেখানোর জন্য।  
সাধারণত Modal তৈরিতে ব্যবহার হয়।

### Syntax:

| Prefix | মানে |
|--------|------|
| `(.)` | Same level |
| `(..)` | One level up |
| `(..)(..)` | Two levels up |
| `(...)` | Root level |

### File Structure (Modal উদাহরণ):

```
app/
├── feed/
│   └── page.tsx               → /feed
└── photo/
    ├── [id]/
    │   └── page.tsx            → /photo/1 (full page)
    └── (.)photo/
        └── [id]/
            └── page.tsx        → /feed থেকে গেলে modal হিসেবে দেখাবে
```

---

## 🧠 সব Routing একনজরে

| Type | Folder নাম | URL উদাহরণ |
|------|-----------|------------|
| Basic | `about/` | `/about` |
| Nested | `dashboard/settings/` | `/dashboard/settings` |
| Dynamic | `[id]/` | `/products/5` |
| Nested Dynamic | `[cat]/[id]/` | `/blog/tech/123` |
| Catch-All | `[...slug]/` | `/docs/a/b/c` |
| Optional Catch-All | `[[...slug]]/` | `/shop` বা `/shop/red` |
| Route Group | `(group)/` | URL-এ আসে না |
| Parallel | `@analytics/` | একই URL-এ একাধিক view |
| Intercepting | `(.)photo/` | Modal-style routing |
