# Next.js App Router → TanStack Router

Migration guide from Next.js App Router to TanStack Router (file-based).

## Overview

| Item          | Value                                      |
| ------------- | ------------------------------------------ |
| Difficulty    | High                                       |
| Automatable   | 40~50%                                     |
| Main work     | Route structure + hooks + remove server components |

---

## Core Changes

| Next.js App Router   | TanStack Router              |
| -------------------- | ---------------------------- |
| `app/` directory     | `src/routes/` directory      |
| `page.tsx`           | `index.tsx` or `route.tsx`   |
| `layout.tsx`         | Parent route with `<Outlet />`|
| `[id]` (dynamic)     | `$id`                        |
| `"use client"`       | Not needed (default client)  |
| `next/navigation`    | `@tanstack/react-router`     |

---

## Route Structure Conversion

### Next.js App Router

```
app/
├── page.tsx                    → /
├── login/page.tsx              → /login
├── signup/page.tsx             → /signup
├── admin/
│   ├── layout.tsx              → Shared layout
│   ├── contents/page.tsx       → /admin/contents
│   ├── displays/page.tsx       → /admin/displays
│   ├── exposure/page.tsx       → /admin/exposure
│   └── profile/page.tsx        → /admin/profile
└── player/[id]/page.tsx        → /player/:id
```

### TanStack Router (file-based)

```
src/routes/
├── __root.tsx                  → Root layout
├── index.tsx                   → /
├── login.tsx                   → /login
├── signup.tsx                  → /signup
├── admin/
│   ├── route.tsx               → /admin layout (with Outlet)
│   ├── contents.tsx            → /admin/contents
│   ├── displays.tsx            → /admin/displays
│   ├── exposure.tsx            → /admin/exposure
│   └── profile.tsx             → /admin/profile
└── player/
    └── $id.tsx                 → /player/:id
```

---

## File Conversion

### Root Layout (__root.tsx)

```tsx
// Next.js app/layout.tsx
import {Inter} from 'next/font/google';
import './globals.css';

const inter = Inter({subsets: ['latin']});

export default function RootLayout({children}: {children: React.ReactNode}) {
  return (
    <html lang="ko">
      <body className={inter.className}>{children}</body>
    </html>
  );
}

// TanStack Router src/routes/__root.tsx
import {createRootRoute, Outlet} from '@tanstack/react-router';
import {ThemeProvider} from '@l-fin/ui-components';

export const Route = createRootRoute({
  component: RootLayout,
});

function RootLayout() {
  return (
    <ThemeProvider>
      <Outlet />
    </ThemeProvider>
  );
}
```

### Regular Page

```tsx
// Next.js app/login/page.tsx
'use client';

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

### Layout Route

```tsx
// Next.js app/admin/layout.tsx
'use client';

export default function AdminLayout({children}: {children: React.ReactNode}) {
  return (
    <div className="admin-layout">
      <Sidebar />
      <main>{children}</main>
    </div>
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
```

### Dynamic Route

```tsx
// Next.js app/player/[id]/page.tsx
'use client';

import {useParams} from 'next/navigation';

export default function PlayerPage() {
  const {id} = useParams();
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

---

## Hook Conversion

### useRouter

```tsx
// Next.js
import {useRouter} from 'next/navigation';

const router = useRouter();
router.push('/dashboard');
router.replace('/login');
router.back();

// TanStack Router
import {useNavigate} from '@tanstack/react-router';

const navigate = useNavigate();
navigate({to: '/dashboard'});
navigate({to: '/login', replace: true});
window.history.back(); // or navigate({ to: -1 })
```

### useParams

```tsx
// Next.js
import {useParams} from 'next/navigation';
const {id} = useParams();

// TanStack Router
// Inside route component
const {id} = Route.useParams();

// Or generic hook
import {useParams} from '@tanstack/react-router';
const {id} = useParams({from: '/player/$id'});
```

### useSearchParams

```tsx
// Next.js
import {useSearchParams} from 'next/navigation';
const searchParams = useSearchParams();
const query = searchParams.get('q');

// TanStack Router
import {useSearch} from '@tanstack/react-router';
const {q} = useSearch({from: '/search'});
```

### usePathname

```tsx
// Next.js
import {usePathname} from 'next/navigation';
const pathname = usePathname();

// TanStack Router
import {useLocation} from '@tanstack/react-router';
const {pathname} = useLocation();
```

---

## Link Component

```tsx
// Next.js
import Link from 'next/link';

<Link href="/about">About</Link>
<Link href={`/user/${id}`}>User</Link>

// TanStack Router
import { Link } from '@tanstack/react-router';

<Link to="/about">About</Link>
<Link to="/user/$id" params={{ id }}>User</Link>
```

---

## Server Component Removal

### Data Fetching

```tsx
// Next.js (Server Component)
async function Page() {
  const data = await fetch('https://api.example.com/data');
  return <div>{data}</div>;
}

// TanStack Router + TanStack Query
import {useQuery} from '@tanstack/react-query';

function Page() {
  const {data, isLoading} = useQuery({
    queryKey: ['data'],
    queryFn: () => fetch('https://api.example.com/data').then((r) => r.json()),
  });

  if (isLoading) return <Loader />;
  return <div>{data}</div>;
}
```

### Route Loader (Alternative)

```tsx
// TanStack Router route loader
export const Route = createFileRoute('/dashboard')({
  loader: async () => {
    const data = await fetchDashboardData();
    return {data};
  },
  component: DashboardPage,
});

function DashboardPage() {
  const {data} = Route.useLoaderData();
  return <div>{data}</div>;
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
});
```

### main.tsx

```tsx
import {StrictMode} from 'react';
import {createRoot} from 'react-dom/client';
import {RouterProvider, createRouter} from '@tanstack/react-router';
import {routeTree} from './routeTree.gen';

const router = createRouter({routeTree});

createRoot(document.getElementById('root')!).render(
  <StrictMode>
    <RouterProvider router={router} />
  </StrictMode>
);
```

---

## Removal Targets

### "use client" Directive

React SPA is client-side by default, remove all `"use client"`:

```tsx
// Before
'use client';

export default function Page() { ... }

// After
export default function Page() { ... }
```

### Next.js-specific Imports

```tsx
// Remove
import Image from 'next/image';
import Link from 'next/link';
import {useRouter, useParams, useSearchParams, usePathname} from 'next/navigation';
import {Metadata} from 'next';

// Replace with
import {Image} from '@l-fin/ui-components'; // or <img>
import {Link, useNavigate, useParams, useSearch, useLocation} from '@tanstack/react-router';
```

---

## Search Commands

```bash
# Find Next.js route files
Glob pattern="app/**/page.tsx"
Glob pattern="app/**/layout.tsx"

# Find next/navigation usage
Grep pattern="from ['\"]next/navigation['\"]" glob="*.{ts,tsx}" path="src/"

# Find next/link usage
Grep pattern="from ['\"]next/link['\"]" glob="*.{ts,tsx}" path="src/"

# Find "use client"
Grep pattern="['\"]use client['\"]" glob="*.{ts,tsx}" path="src/"
```

---

## Removal Targets

Remove after migration:

1. **Directories**: `app/` (Next.js App Router directory)
2. **Files**: `next.config.js`, `next-env.d.ts`
3. **Packages**:
   - `next`
   - `next-themes` (use Mantine theme instead)

```bash
pnpm remove next next-themes
pnpm add @tanstack/react-router @tanstack/router-plugin
```

---

## Notes

1. **Dynamic route syntax**: `[id]` → `$id`
2. **Layout**: `layout.tsx` → parent `route.tsx` with `<Outlet />`
3. **No Server Components**: All data fetching is client-side (TanStack Query recommended)
4. **"use client" not needed**: React SPA is client by default
5. **File naming**: `page.tsx` → `index.tsx` or `route.tsx`
6. **routeTree.gen.ts**: Auto-generated by TanStack Router, do not edit manually
