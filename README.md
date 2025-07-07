# Introduction to Next.js + TanStack for Programmers: Building Axxon

By Xavier Campos

### Introduction

If you've ever wanted to build a full-featured productivity app with a modern developer experience, you've likely come across React and maybe even Next.js. In my capstone project, I'm building Axxon — an all-in-one productivity platform inspired by Notion, Trello, and Monday.com. The goal is to provide solo developers, small teams, organizations, and everyday individuals with a centralized platform to manage tasks, habits, communication, and performance analytics.

To make this happen, I’m using Next.js and TanStack Router with TypeScript, and a backend powered by PostgreSQL via Knex.js. The tech stack is chosen for its scalability, user caching, client-side routing, and ability to support server-side rendering with Type safety from TypeScript. TanStack was also selected due to its capabilities with UI design. This blog is intended for developers who are already comfortable with JavaScript and want to leap into building more robust, interactive, and type-safe applications.

## Why Next.js and TanStack?

Next.js is a popular React framework developed by Vercel that makes it easy to build both static and server-rendered React applications. Its features include file-based routing, API routes, server-side rendering (SSR), and optimized performance. In many ways, it takes care of the boilerplate you'd otherwise spend hours messing with.

TanStack Router, on the other hand, is a newer and more flexible router built for React applications. It replaces traditional routing solutions like React Router with something more scalable, type-safe, and composable.TanStack Routers offer the capability to utilize caching with `useQuery()` and CRUD modifications with caching entirely from these routes with methods like `useMutation()`, which can trigger mutations on cached queries, and `useQueryClient()`, which can allow you to directly interact with the query cache. Due to these features and many additional capabilities, TanStack is gaining recognition as a framework that offers tight TypeScript integration as a TypeScript-first framework with powerful UI capabilities.

## Core Concepts

### Framework Setup 
In order to create a working environment you must start off by creating the app:
```bash
npx create-next-app@latest your-app-name --typescript
```
Then you need to go into your app and install all of the packages: 

```bash
cd your-app-name
npm install
```
Then you can install tanstack routers by doing the following:
```bash
npm install @tanstack/react-router
```

And then configure the apps routes by doing the following:
```bash
// app/router.tsx
import { createRouter, RouterProvider } from '@tanstack/react-router';

const router = createRouter({
  // your route tree here
});

export function AppRouter() {
  return <RouterProvider router={router} />;
}

```
Doing so allows you to define routes using the route files or nested route trees.

### Freamwork and Programming language features/concepts

Typescript when paired with Next.js can utilize NextResponses and NextRequests on controllers which offers additional features for each of them like 
`req.Cookies()` which is used with NextRequest and is useful esaily adding cookies, or `NextResponse.rewrite()` which rewrites the request onto another route.

```ts
// app/api/set-cookie/route.ts
import { NextRequest, NextResponse } from 'next/server';

export async function GET(req: NextRequest) {
  const res = NextResponse.json({ message: 'Cookie set and path rewritten!' });

  // Example: set a cookie using NextRequest & NextResponse
  res.cookies.set('auth_token', 'secure_jwt_token', {
    httpOnly: true,
    secure: process.env.NODE_ENV === 'production',
    maxAge: 60 * 60 * 24 * 7, // 7 days
    path: '/',
  });

  // Example: rewrite request to another route (like redirect but internal)
  return NextResponse.rewrite(new URL('/dashboard', req.url));
}
```
<hr>
Here's an example of Using the aforementioned TanStack Routes with Caching and Mutation with axios to fetch from specific routes 

```tsx
// routes/users.tsx
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { Link } from '@tanstack/react-router';
import axios from 'axios';

export function UsersPage() {
  const queryClient = useQueryClient();

  // Fetch all users
  const { data: users, isLoading } = useQuery({
    queryKey: ['users'],
    queryFn: () => axios.get('/api/users').then(res => res.data),
  });

  // Add a new user
  const mutation = useMutation({
    mutationFn: (newUser: { name: string }) =>
      axios.post('/api/users', newUser),
    onSuccess: () => {
      // Refetch users after  successful mutation
      queryClient.invalidateQueries({ queryKey: ['users'] });
    },
  });

  return (
    <div>
      <h2>Users</h2>
      <ul>
        {users?.map((user: any) => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
      <button
        onClick={() => mutation.mutate({ name: 'New User' })}
        className="bg-blue-500 px-4 py-2 rounded text-white"
      >
        Add User
      </button>
    </div>
  );
}
```

* `useQuery` allows user data to be cached to prevent unnecessary refetching by utilizing.
* `usMutation` triggers a post request to update user data.
* `useQueryClient` gives you direct control to the chache and allows you to invalidate users if its out of date so that a new refetch can be triggered if the list of users is modified 

All of this works well with the TypeSafe functionality from TypeScript, resulting in a unified format for routing and managing data.


# Compare and Contrast

### Why Not Use React Router?

React Router has always been the go-to for client-side routing, but it has a steeper learning curve when it comes to nested layouts, which is something I plan on utilizing, and it doesn’t integrate with TypeScript as seamlessly as TanStack Router does. In an app that I plan to make large-scale, like Axxon, TanStack provides more control over route data, layout nesting, and data fetching patterns. Plus, it pairs nicely with Next.js' `app/` directory structure if used correctly.

### Next.js vs Express

In the Core Curriculum, we built REST APIs using Express, which is great for backend-first apps. However, in projects like Axxon, where frontend and backend are tightly coupled ( like with OAuth or SSR), Next.js allows us to define backend API routes alongside frontend pages. This makes development feel more cohesive and reduces mental overhead.

### Why use TypeScript

For many reasons, TypeScript is better than JavaScript when learning to make an application that you want to troubleshoot, develop, and scale with ease. The reason I'm utilizing TS for this project is that TanStack is a TypeScript-first framework that loses many benefits if not used. Alongside that, Next.js is a framework that works extraordinarily well with Next.js. The typesafe features, the easy configuration, and the amazing developer experience when troubleshooting have made TypeScript a better experience than working with JavaScript, since it's a superset of JavaScript, allowing you to develop in a similar approach.

# Final Thoughts & Tips for Learning

Learning Next.js with TypeScript and TanStack might seem intimidating at first, but the payoff is worth it. You get full-stack flexibility, type safety, and tools that work hand in hand with your app's complexity. If you're looking to build something like my app, these tools are an excellent foundation for so many reasons.

Here are some tips for building an app with this tech stack:

* Use `.env.local` for your local secrets like OAuth keys and JWT secrets since Next.js utilizes this for confidential keys.

* Learn TypeScript gradually. Start by defining `Props` and `Response types` alongside comparing it to javascript as a way to build yourself up.

* Plan out every aspect of your app since utilizing two freamworks can be tedious.

### Resources
* [Next.js Docs](https://nextjs.org/docs)
* [Tanstack Start](https://tanstack.com/start/latest)
* [CodeCademy TypeScript course](https://www.codecademy.com/learn/learn-typescript)
