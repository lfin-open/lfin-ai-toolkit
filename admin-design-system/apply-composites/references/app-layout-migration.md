# AppLayout Migration Guide

Migrate custom sidebar + header layouts to the `AppLayout` component.

## Migration Target Identification

Files are migration targets if they contain:

```tsx
// Pattern 1: Custom layout structure
<div className="flex h-screen">
  <aside className="w-64">
    <nav>{/* navigation menu */}</nav>
  </aside>
  <div className="flex-1">
    <header>{/* header */}</header>
    <main>{/* content */}</main>
  </div>
</div>

// Pattern 2: Mantine AppShell
<AppShell
  header={{ height: 60 }}
  navbar={{ width: 240, breakpoint: 'sm' }}
>
  <AppShell.Header>{/* header */}</AppShell.Header>
  <AppShell.Navbar>{/* sidebar */}</AppShell.Navbar>
  <AppShell.Main>{/* content */}</AppShell.Main>
</AppShell>
```

---

## Basic Migration

### Before

```tsx
import {AppShell, Burger, Group, NavLink} from '@mantine/core';
import {useDisclosure} from '@mantine/hooks';

function Layout({children}) {
  const [opened, {toggle}] = useDisclosure();
  const location = useLocation();
  const navigate = useNavigate();

  return (
    <AppShell
      header={{height: 60}}
      navbar={{width: 240, breakpoint: 'sm', collapsed: {mobile: !opened}}}
    >
      <AppShell.Header>
        <Group h="100%" px="md">
          <Burger opened={opened} onClick={toggle} hiddenFrom="sm" />
          <Logo />
        </Group>
      </AppShell.Header>

      <AppShell.Navbar p="md">
        <NavLink
          label="대시보드"
          leftSection={<IconDashboard />}
          active={location.pathname === '/'}
          onClick={() => navigate('/')}
        />
        <NavLink
          label="사용자"
          leftSection={<IconUsers />}
          active={location.pathname === '/users'}
          onClick={() => navigate('/users')}
        />
        <NavLink
          label="설정"
          leftSection={<IconSettings />}
          active={location.pathname === '/settings'}
          onClick={() => navigate('/settings')}
        />
      </AppShell.Navbar>

      <AppShell.Main>{children}</AppShell.Main>
    </AppShell>
  );
}
```

### After

```tsx
import {AppLayout, PageLayout} from '@l-fin/ui-components';
import {IconDashboard, IconUsers, IconSettings} from '@tabler/icons-react';

const navItems = [
  {key: 'dashboard', label: '대시보드', icon: <IconDashboard size={20} />, href: '/'},
  {key: 'users', label: '사용자', icon: <IconUsers size={20} />, href: '/users'},
  {key: 'settings', label: '설정', icon: <IconSettings size={20} />, href: '/settings'},
];

function Layout({children}) {
  const location = useLocation();
  const navigate = useNavigate();

  return (
    <AppLayout
      sidebar={{
        logo: {
          image: <Logo />,
          text: 'Admin',
          onClick: () => navigate('/'),
        },
        items: navItems,
      }}
      activePath={location.pathname}
      onNavigate={(path) => navigate(path)}
    >
      <PageLayout>{children}</PageLayout>
    </AppLayout>
  );
}
```

---

## Nested Navigation

### Before

```tsx
<AppShell.Navbar>
  <NavLink label="사용자 관리" leftSection={<IconUsers />} defaultOpened>
    <NavLink label="사용자 목록" onClick={() => navigate('/users')} />
    <NavLink label="권한 관리" onClick={() => navigate('/users/roles')} />
    <NavLink label="그룹 관리" onClick={() => navigate('/users/groups')} />
  </NavLink>
</AppShell.Navbar>
```

### After

```tsx
const navItems = [
  {
    key: 'users',
    label: '사용자 관리',
    icon: <IconUsers size={20} />,
    children: [
      {key: 'user-list', label: '사용자 목록', href: '/users'},
      {key: 'user-roles', label: '권한 관리', href: '/users/roles'},
      {key: 'user-groups', label: '그룹 관리', href: '/users/groups'},
    ],
  },
];

<AppLayout
  sidebar={{items: navItems}}
  activePath={location.pathname}
  onNavigate={(path) => navigate(path)}
/>;
```

---

## Grouped Navigation

### Before

```tsx
<AppShell.Navbar>
  <Text size="xs" fw={500} c="dimmed" px="md">
    주요 메뉴
  </Text>
  <NavLink label="대시보드" />
  <NavLink label="분석" />

  <Divider my="sm" />

  <Text size="xs" fw={500} c="dimmed" px="md">
    관리
  </Text>
  <NavLink label="사용자" />
  <NavLink label="설정" />
</AppShell.Navbar>
```

### After

```tsx
const navGroups = [
  {
    key: 'main',
    label: '주요 메뉴',
    items: [
      {key: 'dashboard', label: '대시보드', icon: <IconDashboard />, href: '/'},
      {key: 'analytics', label: '분석', icon: <IconChartBar />, href: '/analytics'},
    ],
  },
  {
    key: 'management',
    label: '관리',
    items: [
      {key: 'users', label: '사용자', icon: <IconUsers />, href: '/users'},
      {key: 'settings', label: '설정', icon: <IconSettings />, href: '/settings'},
    ],
  },
];

<AppLayout sidebar={{groups: navGroups}} />;
```

---

## Resizable Sidebar

### Before (manual implementation)

```tsx
const [sidebarWidth, setSidebarWidth] = useState(240);
// ... resize handler implementation
```

### After

```tsx
<AppLayout
  sidebar={{
    items: navItems,
    resizable: true,
    resizableOptions: {
      initialWidth: 280,
      minWidth: 200,
      maxWidthPercent: 50,
      collapsedWidth: 72,
      storageKey: 'sidebar-width', // localStorage persistence
      withKnob: true,
    },
  }}
/>
```

---

## Header Customization

### Before

```tsx
<AppShell.Header>
  <Group h="100%" px="md" justify="space-between">
    <Group>
      <Burger />
      <TextInput placeholder="검색..." leftSection={<IconSearch />} />
    </Group>
    <Group>
      <ActionIcon>
        <IconBell />
      </ActionIcon>
      <Avatar src={user.avatar} />
    </Group>
  </Group>
</AppShell.Header>
```

### After

```tsx
<AppLayout
  header={{
    search: {
      placeholder: '검색...',
      onSearch: handleSearch,
    },
    actions: (
      <Group gap="sm">
        <ActionIcon variant="subtle">
          <IconBell size={20} />
        </ActionIcon>
        <Avatar src={user.avatar} size="sm" />
      </Group>
    ),
  }}
  sidebar={{items: navItems}}
/>
```

---

## Footer Section

```tsx
<AppLayout
  sidebar={{
    items: navItems,
    footer: (
      <Stack gap="xs">
        <Text size="xs" c="dimmed">
          v1.2.3
        </Text>
        <Button variant="subtle" leftSection={<IconLogout />} onClick={logout}>
          로그아웃
        </Button>
      </Stack>
    ),
  }}
/>
```

---

## TanStack Router Integration

```tsx
// routes/__root.tsx
import {AppLayout} from '@l-fin/ui-components';
import {Outlet, useLocation, useNavigate} from '@tanstack/react-router';

export function RootLayout() {
  const location = useLocation();
  const navigate = useNavigate();

  return (
    <AppLayout
      sidebar={{items: navItems}}
      activePath={location.pathname}
      onNavigate={(path) => navigate({to: path})}
    >
      <Outlet />
    </AppLayout>
  );
}
```

---

## Checklist

- [ ] `AppShell` → `AppLayout` conversion
- [ ] `NavLink` → `navItems` array conversion
- [ ] Nested menus → `children` property
- [ ] Grouped menus → `groups` property
- [ ] Header customization → `header` prop
- [ ] Remove resize logic → use `resizable` option
- [ ] Connect `activePath` and `onNavigate`
- [ ] Remove manual burger menu handling
