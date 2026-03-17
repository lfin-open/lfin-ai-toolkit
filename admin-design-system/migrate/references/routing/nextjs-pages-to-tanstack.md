# Next.js Pages Router → TanStack Router

Migration guide from Next.js Pages Router to TanStack Router (file-based).

## Overview

| Item          | Value                                        |
| ------------- | -------------------------------------------- |
| Difficulty    | High                                         |
| Automatable   | 40~50%                                       |
| Main work     | Route structure + hooks + remove getServerSideProps |

---

## Core Changes

| Next.js Pages Router        | TanStack Router          |
| --------------------------- | ------------------------ |
| `pages/` directory          | `src/routes/` directory  |
| `index.tsx`                 | `index.tsx` (same)       |
| `_app.tsx`                  | `__root.tsx`             |
| `_document.tsx`             | `index.html` (Vite)      |
| `[id].tsx` (dynamic route)  | `$id.tsx`                |
| `[...slug].tsx` (catch-all) | `$.tsx`                  |
| `next/router`               | `@tanstack/react-router` |

---

## Route Structure Conversion

### Next.js Pages Router

```
pages/
├── _app.tsx                    → Global layout
├── _document.tsx               → HTML document structure
├── index.tsx                   → /
├── login.tsx                   → /login
├── signup.tsx                  → /signup
├── admin/
│   ├── index.tsx               → /admin
│   ├── contents.tsx            → /admin/contents
│   └── [id].tsx                → /admin/:id
├── player/
│   └── [id].tsx                → /player/:id
└── posts/
    └── [...slug].tsx           → /posts/* (catch-all)
```

### TanStack Router (file-based)

```
src/routes/
├── __root.tsx                  → Root layout
├── index.tsx                   → /
├── login.tsx                   → /login
├── signup.tsx                  → /signup
├── admin/
│   ├── route.tsx               → /admin layout
│   ├── index.tsx               → /admin
│   ├── contents.tsx            → /admin/contents
│   └── $id.tsx                 → /admin/:id
├── player/
│   └── $id.tsx                 → /player/:id
└── posts/
    └── $.tsx                   → /posts/* (catch-all)
```

---

## File Conversion

### _app.tsx → __root.tsx

```tsx
// Next.js pages/_app.tsx
import type {AppProps} from 'next/app';
import {QueryClient, QueryClientProvider} from '@tanstack/react-query';
import '../styles/globals.css';

const queryClient = new QueryClient();

export default function App({Component, pageProps}: AppProps) {
  return (
    <QueryClientProvider client={queryClient}>
      <Component {...pageProps} />
    </QueryClientProvider>
  );
}

// TanStack Router src/routes/__root.tsx
import {createRootRoute, Outlet} from '@tanstack/react-router';
import {QueryClient, QueryClientProvider} from '@tanstack/react-query';
import {ThemeProvider} from '@l-fin/ui-components';

const queryClient = new QueryClient();

export const Route = createRootRoute({
  component: RootLayout,
});

function RootLayout() {
  return (
    <QueryClientProvider client={queryClient}>
      <ThemeProvider>
        <Outlet />
      </ThemeProvider>
    </QueryClientProvider>
  );
}
```

### Regular Page

```tsx
// Next.js pages/login.tsx
export default function LoginPage() {
  return <div>Login</div>;
}

// TanStack Router src/routes/login.tsx
import {createFileRoute} from '@tanstack/react-router';

export const Route = createFileRoute('/login')({
  component: LoginPage,
});

function LoginPage() {
  return <div>Login</div>;
}
```

### Dynamic Route

```tsx
// Next.js pages/player/[id].tsx
import {useRouter} from 'next/router';

export default function PlayerPage() {
  const router = useRouter();
  const {id} = router.query;

  return <div>Player: {id}</div>;
}

// TanStack Router src/routes/player/$id.tsx
import {createFileRoute} from '@tanstack/react-router';

export const Route = createFileRoute('/player/$id')({
  component: PlayerPage,
});

function PlayerPage() {
  const {id} = Route.useParams();
  return <div>Player: {id}</div>;
}
```

### Catch-all Route

```tsx
// Next.js pages/posts/[...slug].tsx
import {useRouter} from 'next/router';

export default function PostPage() {
  const router = useRouter();
  const {slug} = router.query; // ['a', 'b', 'c'] for /posts/a/b/c

  return <div>Path: {Array.isArray(slug) ? slug.join('/') : slug}</div>;
}

// TanStack Router src/routes/posts/$.tsx
import {createFileRoute} from '@tanstack/react-router';

export const Route = createFileRoute('/posts/$')({
  component: PostPage,
});

function PostPage() {
  const {_splat} = Route.useParams();
  return <div>Path: {_splat}</div>;
}
```

### Layout (Shared UI)

```tsx
// Next.js - pages/admin sublayout handled in each page
// pages/admin/index.tsx
import AdminLayout from '@/layouts/AdminLayout';

export default function AdminPage() {
  return (
    <AdminLayout>
      <div>Admin Dashboard</div>
    </AdminLayout>
  );
}

// TanStack Router src/routes/admin/route.tsx
import {createFileRoute, Outlet} from '@tanstack/react-router';

export const Route = createFileRoute('/admin')({
  component: AdminLayout,
});

function AdminLayout() {
  return (
    <div className="admin-layout">
      <Sidebar />
      <main>
        <Outlet />
      </main>
    </div>
  );
}

// src/routes/admin/index.tsx (layout auto-applied)
import {createFileRoute} from '@tanstack/react-router';

export const Route = createFileRoute('/admin/')({
  component: AdminPage,
});

function AdminPage() {
  return <div>Admin Dashboard</div>;
}
```

---

## Hook Conversion

### useRouter

```tsx
// Next.js
import {useRouter} from 'next/router';

const router = useRouter();
router.push('/dashboard');
router.replace('/login');
router.back();
router.reload();

// TanStack Router
import {useNavigate, useRouter} from '@tanstack/react-router';

const navigate = useNavigate();
navigate({to: '/dashboard'});
navigate({to: '/login', replace: true});
window.history.back();
window.location.reload();

// Current route info
const router = useRouter();
console.log(router.state.location.pathname);
```

### router.query (params + search)

```tsx
// Next.js - query contains both params and search params mixed
import {useRouter} from 'next/router';

const router = useRouter();
const {id, tab} = router.query; // /player/123?tab=info → { id: '123', tab: 'info' }

// TanStack Router - params and search separated
// Route params
const {id} = Route.useParams(); // /player/123 → { id: '123' }

// Search params
const {tab} = Route.useSearch(); // ?tab=info → { tab: 'info' }
```

### router.pathname

```tsx
// Next.js
import {useRouter} from 'next/router';

const router = useRouter();
console.log(router.pathname); // '/player/[id]' (pattern)
console.log(router.asPath); // '/player/123?tab=info' (actual path)

// TanStack Router
import {useLocation} from '@tanstack/react-router';

const location = useLocation();
console.log(location.pathname); // '/player/123' (actual path)
console.log(location.search); // { tab: 'info' }
```

### router.isReady

```tsx
// Next.js - check client hydration complete
import {useRouter} from 'next/router';

const router = useRouter();

useEffect(() => {
  if (router.isReady) {
    // query values available
    console.log(router.query.id);
  }
}, [router.isReady]);

// TanStack Router - SPA so always ready
// No isReady check needed, use directly
const {id} = Route.useParams();
```

---

## Link Component

```tsx
// Next.js
import Link from 'next/link';

<Link href="/about">About</Link>
<Link href={`/user/${id}`}>User</Link>
<Link href={{ pathname: '/search', query: { q: 'test' } }}>Search</Link>

// TanStack Router
import { Link } from '@tanstack/react-router';

<Link to="/about">About</Link>
<Link to="/user/$id" params={{ id }}>User</Link>
<Link to="/search" search={{ q: 'test' }}>Search</Link>
```

---

## Data Fetching Conversion

### getServerSideProps

```tsx
// Next.js pages/dashboard.tsx
export async function getServerSideProps() {
  const data = await fetchDashboardData();
  return {props: {data}};
}

export default function Dashboard({data}) {
  return <div>{data.title}</div>;
}

// TanStack Router + TanStack Query (recommended)
import {createFileRoute} from '@tanstack/react-router';
import {useQuery} from '@tanstack/react-query';

export const Route = createFileRoute('/dashboard')({
  component: Dashboard,
});

function Dashboard() {
  const {data, isLoading} = useQuery({
    queryKey: ['dashboard'],
    queryFn: fetchDashboardData,
  });

  if (isLoading) return <Loader />;
  return <div>{data.title}</div>;
}
```

### getStaticProps / getStaticPaths

```tsx
// Next.js - SSG
export async function getStaticPaths() {
  const posts = await getPosts();
  return {
    paths: posts.map((post) => ({params: {id: post.id}})),
    fallback: false,
  };
}

export async function getStaticProps({params}) {
  const post = await getPost(params.id);
  return {props: {post}};
}

// TanStack Router - Route Loader
export const Route = createFileRoute('/posts/$id')({
  loader: async ({params}) => {
    const post = await getPost(params.id);
    return {post};
  },
  component: PostPage,
});

function PostPage() {
  const {post} = Route.useLoaderData();
  return <div>{post.title}</div>;
}
```

---

## Search Params Type Definition

```tsx
// TanStack Router - type-safe search params
import {createFileRoute} from '@tanstack/react-router';
import {z} from 'zod';

const searchSchema = z.object({
  page: z.number().optional().default(1),
  sort: z.enum(['asc', 'desc']).optional().default('desc'),
  q: z.string().optional(),
});

export const Route = createFileRoute('/search')({
  validateSearch: searchSchema,
  component: SearchPage,
});

function SearchPage() {
  const {page, sort, q} = Route.useSearch();
  // page: number, sort: 'asc' | 'desc', q: string | undefined
  return <div>...</div>;
}
```

---

## Config Files

### vite.config.ts

```ts
import {defineConfig} from 'vite';
import react from '@vitejs/plugin-react';
import {TanStackRouterVite} from '@tanstack/router-plugin/vite';

export default defineConfig({
  plugins: [TanStackRouterVite(), react()],
  resolve: {
    alias: {
      '@': '/src',
    },
  },
});
```

### main.tsx

```tsx
import {StrictMode} from 'react';
import {createRoot} from 'react-dom/client';
import {RouterProvider, createRouter} from '@tanstack/react-router';
import {routeTree} from './routeTree.gen';
import './index.css';

const router = createRouter({routeTree});

// Type safety declaration
declare module '@tanstack/react-router' {
  interface Register {
    router: typeof router;
  }
}

createRoot(document.getElementById('root')!).render(
  <StrictMode>
    <RouterProvider router={router} />
  </StrictMode>
);
```

### index.html (Vite)

```html
<!DOCTYPE html>
<html lang="ko">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>App</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>
```

---

## Removal Targets

### Next.js-specific Imports

```tsx
// Remove
import {useRouter} from 'next/router';
import Link from 'next/link';
import Head from 'next/head';
import Image from 'next/image';
import Script from 'next/script';

// Replace with
import {useNavigate, useParams, useSearch, useLocation, Link} from '@tanstack/react-router';
import {Image} from '@l-fin/ui-components'; // or <img>
// Head → Set in index.html or use react-helmet-async
```

### Files to Remove

- `pages/_app.tsx`
- `pages/_document.tsx`
- `next.config.js` / `next.config.mjs`
- `next-env.d.ts`
- `.next/` directory

---

## Search Commands

```bash
# Find Next.js Pages
Glob pattern="pages/**/*.tsx"
Glob pattern="pages/**/*.ts"

# Find next/router usage
Grep pattern="from ['\"]next/router['\"]" glob="*.{ts,tsx}" path="src/"
Grep pattern="from ['\"]next/router['\"]" glob="*.{ts,tsx}" path="pages/"

# Find next/link usage
Grep pattern="from ['\"]next/link['\"]" glob="*.{ts,tsx}" path="src/"

# Find getServerSideProps
Grep pattern="getServerSideProps" glob="*.{ts,tsx}" path="pages/"

# Find getStaticProps
Grep pattern="getStaticProps" glob="*.{ts,tsx}" path="pages/"

# Find useRouter usage
Grep pattern="useRouter\\(\\)" glob="*.{ts,tsx}" path="src/"
```

---

## Removal Targets

Remove after migration:

1. **Directories**: `pages/`, `.next/`
2. **Files**: `next.config.js`, `next.config.mjs`, `next-env.d.ts`
3. **Packages**:
   - `next`
   - `next-themes` (use Mantine theme instead)

```bash
pnpm remove next next-themes
pnpm add @tanstack/react-router @tanstack/router-plugin vite @vitejs/plugin-react
```

---

## Notes

1. **Dynamic route syntax**: `[id]` → `$id`, `[...slug]` → `$`
2. **Query separation**: Next.js `router.query` mixes params and search
   - TanStack Router separates `useParams()` and `useSearch()`
3. **No SSR/SSG**: TanStack Router is SPA-only
   - Replace `getServerSideProps`, `getStaticProps` with TanStack Query or Route Loader
4. **_app.tsx → __root.tsx**: Global Provider location changes
5. **_document.tsx removal**: Set HTML structure in `index.html`
6. **router.isReady not needed**: SPA is always ready
7. **routeTree.gen.ts**: Auto-generated by TanStack Router, do not edit manually
8. **File naming**: `route.tsx` for layout role, `index.tsx` or filename for page role
