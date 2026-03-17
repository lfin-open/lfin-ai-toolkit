# React Router v5 → TanStack Router

Migration guide from React Router v5 (HashRouter) to TanStack Router.

## Overview

| Item          | Value                                  |
| ------------- | -------------------------------------- |
| Difficulty    | Medium~High                            |
| Automatable   | 30~40%                                 |
| Main work     | Route structure + navigation conversion|

---

## Concept Mapping

| React Router v5        | TanStack Router           |
| ---------------------- | ------------------------- |
| `<HashRouter>`         | `createRouter()`          |
| `<Switch>`             | File-based routing        |
| `<Route path="" />`    | `createRoute()` / file    |
| `useHistory()`         | `useNavigate()`           |
| `history.push()`       | `navigate({ to: ... })`   |
| `<Link to="">`         | `<Link to="">`            |
| `useParams()`          | `useParams()`             |
| `useLocation()`        | `useLocation()`           |
| `<Redirect>`           | `redirect()` / `Navigate` |

---

## Router Setup

### Before: HashRouter Setup

```jsx
// containers/App/index.js
import { HashRouter } from 'react-router-dom';
import { Switch, Route } from 'react-router';

function App() {
  return (
    <HashRouter>
      <Switch>
        <Route path="/login" render={(props) => <LoginLayout {...props} />} />
        <Route path="/main" render={(props) => <MainLayout {...props} />} />
        <Route path="/" render={(props) => <IndexLayout {...props} />} />
      </Switch>
    </HashRouter>
  );
}
```

### After: TanStack Router Setup

```tsx
// router.tsx
import { createRouter, createRootRoute, createRoute } from '@tanstack/react-router';
import { RootLayout } from './routes/__root';
import { IndexPage } from './routes/index';
import { LoginPage } from './routes/login';
import { MainLayout } from './routes/main/__layout';

// Root route
const rootRoute = createRootRoute({
  component: RootLayout,
});

// Index route
const indexRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/',
  component: IndexPage,
});

// Login route
const loginRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/login',
  component: LoginPage,
});

// Main layout route
const mainRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/main',
  component: MainLayout,
});

// Route tree
const routeTree = rootRoute.addChildren([
  indexRoute,
  loginRoute,
  mainRoute.addChildren([
    // Main subroutes...
  ]),
]);

// Create router
export const router = createRouter({ routeTree });

// Type registration
declare module '@tanstack/react-router' {
  interface Register {
    router: typeof router;
  }
}
```

```tsx
// main.tsx
import { RouterProvider } from '@tanstack/react-router';
import { router } from './router';

ReactDOM.createRoot(document.getElementById('root')!).render(
  <RouterProvider router={router} />
);
```

---

## File-based Routing (Recommended)

```
src/routes/
├── __root.tsx           # Root layout
├── index.tsx            # /
├── login.tsx            # /login
└── main/
    ├── __layout.tsx     # /main/* layout
    ├── index.tsx        # /main (redirect)
    ├── logs.tsx         # /main/logs
    ├── caches.tsx       # /main/caches
    └── filter/
        ├── index.tsx    # /main/filter
        └── pending.tsx  # /main/filter/pending
```

```tsx
// routes/__root.tsx
import { createRootRoute, Outlet } from '@tanstack/react-router';
import { ThemeProvider } from '@l-fin/ui-components';

export const Route = createRootRoute({
  component: () => (
    <ThemeProvider>
      <Outlet />
    </ThemeProvider>
  ),
});
```

```tsx
// routes/main/__layout.tsx
import { createFileRoute, Outlet } from '@tanstack/react-router';
import { AppLayout } from '@l-fin/ui-components';

export const Route = createFileRoute('/main')({
  component: () => (
    <AppLayout
      header={{ title: 'Geolocation CMS' }}
      navbar={{ items: navItems }}
    >
      <Outlet />
    </AppLayout>
  ),
});
```

---

## Nested Route Conversion

### Before: Switch in Layout

```jsx
// layouts/MainLayout/index.js
import { Route, Switch } from 'react-router';
import getMainRoute from 'routes/MainRoute';

function MainLayout() {
  const routes = getMainRoute().map((item) => (
    <Route
      exact
      path={item.path}
      render={(props) => <item.component {...props} />}
      key={item.path}
    />
  ));

  return (
    <MainLayoutWrapper>
      <Switch>{routes}</Switch>
    </MainLayoutWrapper>
  );
}
```

```jsx
// routes/MainRoute/index.js
import Logs from 'containers/Logs/Loadable';
import Caches from 'containers/Caches/Loadable';

export default function getMainRoute() {
  return [
    { path: '/main/logs', component: Logs },
    { path: '/main/caches', component: Caches },
    { path: '/main/filter/pending', component: PendingAp },
    { path: '/main/filter', component: RegisteredAp },
  ];
}
```

### After: File-based Nested Routes

```tsx
// routes/main/__layout.tsx
import { createFileRoute, Outlet } from '@tanstack/react-router';
import { AppLayout } from '@l-fin/ui-components';

export const Route = createFileRoute('/main')({
  component: MainLayout,
});

function MainLayout() {
  return (
    <AppLayout>
      <Outlet /> {/* Child route rendering */}
    </AppLayout>
  );
}
```

```tsx
// routes/main/logs.tsx
import { createFileRoute } from '@tanstack/react-router';

export const Route = createFileRoute('/main/logs')({
  component: LogsPage,
});

function LogsPage() {
  return <div>Logs Page</div>;
}
```

---

## Navigation Conversion

### Before: useHistory

```jsx
import { useHistory } from 'react-router';

function CommonDrawer() {
  const history = useHistory();

  const onClickItem = (uri) => {
    history.push({ pathname: uri });
  };

  return (
    <ListItem onClick={() => onClickItem('/main/logs')}>
      로그 조회
    </ListItem>
  );
}
```

### After: useNavigate / Link

```tsx
import { useNavigate, Link } from '@tanstack/react-router';

function Sidebar() {
  const navigate = useNavigate();

  const handleClick = () => {
    navigate({ to: '/main/logs' });
  };

  return (
    <>
      {/* Link usage (recommended) */}
      <Link to="/main/logs">로그 조회</Link>

      {/* Or programmatic navigation */}
      <button onClick={handleClick}>로그 조회</button>
    </>
  );
}
```

### NavLink Active State

```tsx
import { Link, useLocation } from '@tanstack/react-router';

function NavItem({ to, children }: { to: string; children: React.ReactNode }) {
  const location = useLocation();
  const isActive = location.pathname === to;

  return (
    <Link
      to={to}
      className={isActive ? 'active' : ''}
      style={{ fontWeight: isActive ? 'bold' : 'normal' }}
    >
      {children}
    </Link>
  );
}

// Or use Link's activeProps
<Link
  to="/main/logs"
  activeProps={{ className: 'active' }}
>
  로그 조회
</Link>
```

---

## Auth Guard

### Before: Redirect in Component

```jsx
// containers/Logs/index.js
import { useHistory } from 'react-router';
import { isValidToken } from 'utils/tokenHelper';

function LogList() {
  const history = useHistory();

  useEffect(() => {
    if (!isValidToken()) {
      history.push('/login');
    }
  }, []);

  // ...
}
```

### After: Route beforeLoad

```tsx
// routes/main/__layout.tsx
import { createFileRoute, redirect } from '@tanstack/react-router';
import { isValidToken } from '@/lib/tokenHelper';

export const Route = createFileRoute('/main')({
  beforeLoad: async () => {
    if (!isValidToken()) {
      throw redirect({ to: '/login' });
    }
  },
  component: MainLayout,
});
```

---

## Query Parameters

### Before: URLSearchParams Direct Usage

```jsx
import { useLocation, useHistory } from 'react-router';

function LogsPage() {
  const location = useLocation();
  const history = useHistory();
  const params = new URLSearchParams(location.search);
  const page = params.get('page') || '1';

  const setPage = (newPage) => {
    params.set('page', newPage);
    history.push({ search: params.toString() });
  };
}
```

### After: validateSearch + useSearch

```tsx
// routes/main/logs.tsx
import { createFileRoute, useSearch, useNavigate } from '@tanstack/react-router';
import { z } from 'zod';

const logsSearchSchema = z.object({
  page: z.number().default(1),
  size: z.number().default(10),
});

export const Route = createFileRoute('/main/logs')({
  validateSearch: logsSearchSchema,
  component: LogsPage,
});

function LogsPage() {
  const { page, size } = useSearch({ from: '/main/logs' });
  const navigate = useNavigate();

  const setPage = (newPage: number) => {
    navigate({
      search: (prev) => ({ ...prev, page: newPage }),
    });
  };

  return <DataTable page={page} onPageChange={setPage} />;
}
```

---

## HashRouter → History Router

Change from HashRouter (`/#/main/logs`) to normal routing (`/main/logs`):

```tsx
// router.tsx
import { createRouter } from '@tanstack/react-router';

export const router = createRouter({
  routeTree,
  // Default is history mode (not hash)
});
```

**Server config required** (SPA fallback):

```ts
// vite.config.ts (dev server)
export default defineConfig({
  server: {
    proxy: {
      '/api': 'http://localhost:3000',
    },
  },
  // Vite handles SPA fallback automatically
});
```

```nginx
# nginx (production)
location / {
  try_files $uri $uri/ /index.html;
}
```

---

## Search Commands

```bash
# Find React Router imports
Grep pattern="from ['\"]react-router" glob="*.{js,jsx}" path="prev/"

# Find useHistory usage
Grep pattern="useHistory" glob="*.{js,jsx}" path="prev/"

# Find history.push usage
Grep pattern="history\\.push" glob="*.{js,jsx}" path="prev/"

# Find Route component
Grep pattern="<Route" glob="*.{js,jsx}" path="prev/"

# Find Link component
Grep pattern="<Link" glob="*.{js,jsx}" path="prev/"
```

---

## Removal Targets

Remove after migration:

1. **Packages**:
   - `react-router`
   - `react-router-dom`
   - `history`
   - `connected-react-router`

2. **Folders/Files**:
   - `routes/` (existing route definitions)
   - `layouts/` (integrate into TanStack Router layouts)

```bash
pnpm remove react-router react-router-dom history connected-react-router
```

---

## Notes

1. **HashRouter removal**: `/#/` URL pattern becomes `/`
2. **Server config**: SPA fallback required (nginx, Apache, etc.)
3. **Type safety**: TanStack Router provides full type safety
4. **Code Splitting**: Use `lazy()` or `component: () => import()`
5. **Data Loading**: Route-level data loading possible via `loader` function
