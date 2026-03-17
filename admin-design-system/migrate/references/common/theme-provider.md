# ThemeProvider Setup Guide

@l-fin/ui-components ThemeProvider setup and existing theme system migration guide.

## Basic Setup

### Minimal Setup

```tsx
// src/main.tsx
import {ThemeProvider} from '@l-fin/ui-components';

function App() {
  return (
    <ThemeProvider>
      <YourApp />
    </ThemeProvider>
  );
}
```

### ThemeProvider Included Features

| Feature           | Description                     | Default   |
| ----------------- | ------------------------------- | --------- |
| `MantineProvider` | Mantine theme context           | Enabled   |
| `Notifications`   | Notification system             | Enabled   |
| `DatesProvider`   | Date components Korean locale   | Enabled   |
| LFin design tokens| Custom colors, typography, etc. | Applied   |

**Important**: Do NOT add `<Notifications />` component separately (duplicate).

---

## Existing Theme System Migration

### MantineProvider → ThemeProvider

```tsx
// Before
import {MantineProvider} from '@mantine/core';
import {Notifications} from '@mantine/notifications';
import {DatesProvider} from '@mantine/dates';
import {theme} from './mantine-theme';

<MantineProvider theme={theme}>
  <DatesProvider settings={{locale: 'ko'}}>
    <Notifications />
    <App />
  </DatesProvider>
</MantineProvider>;

// After
import {ThemeProvider} from '@l-fin/ui-components';

<ThemeProvider>
  <App />
</ThemeProvider>;
```

### next-themes → ThemeProvider

```tsx
// Before (Next.js + next-themes)
import {ThemeProvider as NextThemeProvider} from 'next-themes';

<NextThemeProvider attribute="class" defaultTheme="light">
  <App />
</NextThemeProvider>;

// After
import {ThemeProvider} from '@l-fin/ui-components';

<ThemeProvider defaultColorScheme="light">
  <App />
</ThemeProvider>;
```

### ChakraProvider → ThemeProvider

```tsx
// Before
import { ChakraProvider, extendTheme } from '@chakra-ui/react';

const theme = extendTheme({ ... });

<ChakraProvider theme={theme}>
  <App />
</ChakraProvider>

// After
import { ThemeProvider } from '@l-fin/ui-components';

<ThemeProvider>
  <App />
</ThemeProvider>
```

---

## Custom Theme

### Using createAppTheme

```tsx
import {ThemeProvider, createAppTheme} from '@l-fin/ui-components';

const myTheme = createAppTheme({
  // Mantine theme extension
  mantine: {
    primaryColor: 'teal',
    defaultRadius: 'md',
    fontFamily: 'Pretendard, sans-serif',
  },
});

<ThemeProvider theme={myTheme}>
  <App />
</ThemeProvider>;
```

### Migrating Existing mantine-theme.ts

```tsx
// Before - mantine-theme.ts
import {createTheme, MantineColorsTuple} from '@mantine/core';

const primary: MantineColorsTuple = [
  '#e7f0ff',
  '#cfdeff',
  '#9cbaff',
  '#6493ff',
  '#3873fe',
  '#1d5ffe',
  '#0952fe',
  '#0045e3',
  '#003bcb',
  '#0030b3',
];

export const theme = createTheme({
  primaryColor: 'primary',
  colors: {primary},
  defaultRadius: 'md',
});

// After - using createAppTheme
import {createAppTheme} from '@l-fin/ui-components';

const myTheme = createAppTheme({
  mantine: {
    primaryColor: 'primary',
    colors: {
      primary: [
        '#e7f0ff',
        '#cfdeff',
        '#9cbaff',
        '#6493ff',
        '#3873fe',
        '#1d5ffe',
        '#0952fe',
        '#0045e3',
        '#003bcb',
        '#0030b3',
      ],
    },
    defaultRadius: 'md',
  },
});
```

---

## Dark Mode

### colorScheme Setting

```tsx
// Default light mode
<ThemeProvider defaultColorScheme="light">

// Default dark mode
<ThemeProvider defaultColorScheme="dark">

// Follow system setting
<ThemeProvider defaultColorScheme="auto">
```

### Dynamic colorScheme Change

```tsx
import {useMantineColorScheme} from '@l-fin/ui-components';

function ColorSchemeToggle() {
  const {colorScheme, toggleColorScheme} = useMantineColorScheme();

  return (
    <Button onClick={toggleColorScheme}>
      {colorScheme === 'dark' ? '라이트 모드' : '다크 모드'}
    </Button>
  );
}
```

---

## Removal Targets

Remove after migration:

### 1. Components

```tsx
// Remove
<Notifications />           // Included in ThemeProvider
<DatesProvider>             // Included in ThemeProvider
<MantineProvider>           // Replaced by ThemeProvider
```

### 2. Files

- `mantine-theme.ts` (or similar custom theme file)
- `theme.ts` (Chakra/MUI theme file)

### 3. Packages

```bash
pnpm remove next-themes  # Next.js projects
```

---

## Search Commands

```bash
# Find MantineProvider
Grep pattern="MantineProvider" glob="*.{ts,tsx}" path="src/"

# Find Notifications component
Grep pattern="<Notifications" glob="*.{ts,tsx}" path="src/"

# Find DatesProvider
Grep pattern="DatesProvider" glob="*.{ts,tsx}" path="src/"

# Find next-themes usage
Grep pattern="from ['\"]next-themes['\"]" glob="*.{ts,tsx}" path="src/"

# Find custom theme files
Glob pattern="src/**/*theme*.{ts,tsx}"
```

---

## Notes

1. **No duplicate Notifications**: Included in ThemeProvider, no separate addition needed
2. **Remove DatesProvider**: Included in ThemeProvider with Korean locale
3. **Clean up mantine-theme.ts**: Use default theme or migrate to createAppTheme
4. **Remove next-themes**: Use Mantine's colorScheme system
