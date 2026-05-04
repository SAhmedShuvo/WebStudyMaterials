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
