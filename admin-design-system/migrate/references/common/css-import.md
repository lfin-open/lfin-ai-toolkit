# CSS Import Guide

CSS import configuration for @l-fin/ui-components.

## Basic Setup

### Tailwind + Mantine Project (Recommended)

Add `@import` in `src/index.css`:

```css
/* src/index.css */

/* 1. Tailwind preset - Mantine compatible reset (includes @mantine/core/styles.css) */
@import 'tailwind-preset-mantine';

/* 2. Mantine Notifications styles (if using notifications) */
@import '@mantine/notifications/styles.css';

/* 3. LFin UI Components - design tokens + Mantine extension module styles */
@import '@l-fin/ui-components/styles';

/* 4. LFin UI Components - component styles (CSS modules) */
@import '@l-fin/ui-components/component-styles';
```

### Mantine Only Project (No Tailwind)

Add JS imports in `src/main.tsx`:

```tsx
// src/main.tsx

// 1. Mantine base styles (required)
import '@mantine/core/styles.css';

// 2. Mantine dates styles (if using dates)
import '@mantine/dates/styles.css';

// 3. Mantine notifications styles (if using notifications)
import '@mantine/notifications/styles.css';

// 4. LFin UI Components - design tokens (MUST be after Mantine!)
// eslint-disable-next-line import/order
import '@l-fin/ui-components/styles';
```

---

## Important Notes

### 1. Order Matters

```css
/* ✅ Correct order */
@import 'tailwind-preset-mantine'; /* 1. Includes Mantine reset */
@import '@mantine/notifications/styles.css'; /* 2. Mantine extension */
@import '@l-fin/ui-components/styles'; /* 3. LFin tokens (last!) */
@import '@l-fin/ui-components/component-styles';

/* ❌ Wrong order - LFin tokens get overwritten */
@import '@l-fin/ui-components/styles'; /* LFin first */
@import 'tailwind-preset-mantine'; /* Mantine overwrites */
```

### 2. When Using tailwind-preset-mantine

`tailwind-preset-mantine` includes `@mantine/core/styles.css`, no separate import needed:

```css
/* ✅ With tailwind-preset-mantine */
@import 'tailwind-preset-mantine';
/* No need to import @mantine/core/styles.css separately */

/* ❌ Duplicate import (unnecessary) */
@import '@mantine/core/styles.css';
@import 'tailwind-preset-mantine';
```

### 3. ESLint import/order Error

May get ESLint error with JS imports:

```tsx
// ESLint error suppression needed
import '@mantine/core/styles.css';
// eslint-disable-next-line import/order
import '@l-fin/ui-components/styles';
```

---

## TypeScript Module Declaration

May get TypeScript error on CSS import:

```
Cannot find module '@l-fin/ui-components/styles' or its corresponding type declarations.
```

**Solution**: Add to `src/vite-env.d.ts` (or project type declaration file):

```ts
// src/vite-env.d.ts
/// <reference types="vite/client" />

declare module '@l-fin/ui-components/styles';
declare module '@l-fin/ui-components/component-styles';
```

---

## Removable Items After Migration

### With tailwind-preset-mantine

```css
/* Can remove (included in tailwind-preset-mantine) */
@import '@mantine/core/styles.css';
```

### With @l-fin/ui-components/styles

```css
/* Can remove (included in @l-fin/ui-components/styles) */
@import '@mantine/dates/styles.css';
```

### Final Form

```css
/* src/index.css - minimal config */
@import 'tailwind-preset-mantine';
@import '@mantine/notifications/styles.css'; /* if using notifications */
@import '@l-fin/ui-components/styles';
@import '@l-fin/ui-components/component-styles';
```

---

## Framework-specific Setup

### Vite

```css
/* src/index.css */
@import 'tailwind-preset-mantine';
@import '@mantine/notifications/styles.css';
@import '@l-fin/ui-components/styles';
@import '@l-fin/ui-components/component-styles';
```

```tsx
// src/main.tsx
import './index.css';
```

### Next.js

```tsx
// app/layout.tsx or _app.tsx
import '@mantine/core/styles.css';
import '@mantine/dates/styles.css';
import '@mantine/notifications/styles.css';
// eslint-disable-next-line import/order
import '@l-fin/ui-components/styles';
```

### Create React App

```tsx
// src/index.tsx
import '@mantine/core/styles.css';
import '@mantine/notifications/styles.css';
// eslint-disable-next-line import/order
import '@l-fin/ui-components/styles';
import './index.css'; // app custom styles
```

---

## Verification

Check if CSS is loaded correctly:

1. **Run dev server**: `pnpm dev`
2. **Browser DevTools** → Elements → Styles
3. **CSS variables to check**:
   - `--mantine-color-*` (Mantine colors)
   - `--primary-filled` (LFin tokens)

If LFin tokens not visible, check import order.
