# Frontend Masters: Introduction to Next.js 13+, v3 by Scott Moss

## Introduction
---
Instructor notes: https://scottmoss.notion.site/scottmoss/Intro-to-Next-js-V3-6cefbdba58d94e3897dcb8d7e7fc0337

### Benefits
Benefits of using Next.js:
- Server-side rendering
- Automatic code splitting
- Optimized performance
### Server Components
React Server Components (RSC) were introduced in React 18.
Next.js supports these through their **app directory**.
#### SPA vs SSR
In a Single-Page Application (SPA), you render and fetch data in the browser itself.
In Server Side Rendering (SSR), you render the initial page in the server itself.

In SSR, pages do not become interactive until after the browser finishes downloading the JS bundle that adds interactivity. 

#### RSC
RSC is new type of SSR where there is no need to wait on JavaScript being sent to the browser.
RSC allow developers to write components that run on the server and stream their output to the client.

You still cannot have any interactivity though. That is what client components are for.

RSC is useful for components that need to fetch data on page load. It it handled on the backend and the client displays the resulting HTML.

Next.js uses RSC, but the lines are blurred. It's not easy obvious which components are server side and which components are client side.

Everything defaults to server components. You need to opt in for client components.

## Setup
---
```shell
$ npx create-next-app@latest my-next-13-demo-app
```

Using TypeScript, Tailwind, ESLint, App Router and no src directory for the demo.

`autoprefixer` and `postcss` are needed by Tailwind

You need to use at least Node 18.
### App Router
The "use App Router" option creates an `app` directory.
Make sure you use the "Using App Router" documentation (instead of "Using Pages Router").

## Routing
---
### Adding a Page
The file `/app/dashboard/settings/page.tsx` creates a page at `https://localhost:3000/dashboard/settings`. **It has to be called `page`.** This is not the case for a Pages Router.

The homepage lives at `app/page.tsx`.

Use default exports for any `page.tsx`.

There's no need to restart the dev server after adding a new page.

### Route Grouping
If you want to group page-related items together under `app/`, but don't want to create a new route, wrap the name in parentheses, e.g. `app/(dashboard)`

If you add a `layout.tsx` to `(dashboard)`, every single page at the `(dashboard)` level or below will share that layout.

### Dynamic Routing
To make a route dynamic, use square brackets in the directory name, e.g. `app/docs/[id]`. A page within this directory will load at `https://localhost:3000/docs/234634647`

To access the param:

```jsx
const DocsIdPage = ({ params }) => {
  return <div>id: {params.id}</div>
}
```

When using multiple parameters (`app/docs/[id]/[title]`):

```jsx
const DocsIdPage = ({ params }) => {
  return <div>id: {params.id} title: {params.title}</div>
}
```

#### Catch-All Routes
Use three dots in front of the param: `app/docs/[...id]`

Now, `https://localhost:3000/docs/234634647`, `https://localhost:3000/docs/234634647/sdgsdgsdg` and `https://localhost:3000/docs/234634647/sdgsdgsdg/my-page` will all display that same page.

This is great for documentation pages. Every page is exactly the same except for the content.

Currently, `app/docs/page.tsx` will not work.
If you need to have a page at `app/docs` as well, make the catch-all route `app/docs/[[...id]]`

When reading params via `params`, these will now be an array.

### Additional Functionality
You have access to additional features, such a query parameters, with `import { useRouter } from 'next/router'`. Remember, some of these features will only work in client components.

New: You now have the ability to access query params on the server side with `searchParams`

```tsx
export default function Page({
  params,
  searchParams
}: { 
  params: { slug: string }
  searchParams: { [key: string]: string | string[] | undefined }
}) { 
  return <h1>My Page</h1>
}
```

|URL|`searchParams`|
|---|---|
|`/shop?a=1`|`{ a: '1' }`|
|`/shop?a=1&b=2`|`{ a: '1', b: '2' }`|
|`/shop?a=1&a=2`|`{ a: ['1', '2'] }`|

## Styling
---

## Data Handling
---

## Deployment
---
