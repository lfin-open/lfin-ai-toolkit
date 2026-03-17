# styled-components → Mantine Styles

Migration guide from styled-components to Mantine style system.

## Overview

| Item          | Value                             |
| ------------- | --------------------------------- |
| Difficulty    | Medium                            |
| Automatable   | 40~50%                            |
| Main work     | styled → className/styles prop    |

---

## Styling Options

Available styling methods in Mantine:

| Method          | Use Case                | Example                         |
| --------------- | ----------------------- | ------------------------------- |
| `className`     | Static styles           | CSS Modules, Tailwind           |
| `style` prop    | Inline styles           | `style={{ padding: 8 }}`        |
| `styles` prop   | Component inner styling | `styles={{ root: {...} }}`      |
| Style Props     | Simple layout           | `<Box p="md" bg="gray.1" />`    |
| CSS Variables   | Theme variable reference| `var(--mantine-color-blue-6)`   |

---

## Basic Conversion Pattern

### Simple styled Component

```jsx
// Before: styled-components
import styled from 'styled-components';

const CommonButton = styled.button`
  width: 108px;
  height: 36px;
  padding: 0px 4px;
  margin: 10px;
  border: 1px solid rgb(60, 75, 100);
  border-radius: 8px;
  background-color: transparent;
  color: rgb(60, 75, 100);
  font-size: 12px;
  font-weight: 600;

  :hover {
    cursor: pointer;
    color: white;
    background-color: rgb(50, 65, 88);
  }
`;

export default CommonButton;
```

```tsx
// After: @l-fin/ui-components Button
import { Button } from '@l-fin/ui-components';

// Use default Button (theme styles applied)
<Button variant="outline" size="sm">
  버튼
</Button>

// Or if custom styles needed
<Button
  variant="outline"
  size="sm"
  styles={{
    root: {
      width: 108,
      height: 36,
      fontSize: 12,
      fontWeight: 600,
    },
  }}
>
  버튼
</Button>
```

### Wrapper Component

```jsx
// Before: styled-components
const AppWrapper = styled.div`
  margin: 0 auto;
  display: flex;
  flex-direction: column;
`;
```

```tsx
// After: Mantine Box with style props
import { Box } from '@l-fin/ui-components';

<Box m="0 auto" display="flex" style={{ flexDirection: 'column' }}>
  {children}
</Box>

// Or CSS Modules
// styles.module.css
.appWrapper {
  margin: 0 auto;
  display: flex;
  flex-direction: column;
}

// Component
import styles from './styles.module.css';
<div className={styles.appWrapper}>{children}</div>
```

---

## MUI makeStyles/withStyles Conversion

### makeStyles → CSS Modules or styles prop

```jsx
// Before: MUI makeStyles
import { makeStyles } from '@material-ui/core/styles';

const useStyles = makeStyles({
  drawer: {
    width: 240,
    flexShrink: 0,
  },
  drawerPaper: {
    width: 240,
    backgroundColor: 'rgb(30, 35, 47)',
  },
  drawerTitle: {
    color: 'white',
    fontSize: '16px !important',
    fontWeight: 'bold',
  },
});

function CommonDrawer() {
  const classes = useStyles();
  return (
    <Drawer className={classes.drawer} classes={{ paper: classes.drawerPaper }}>
      <Typography className={classes.drawerTitle}>Title</Typography>
    </Drawer>
  );
}
```

```tsx
// After: CSS Modules
// CommonDrawer.module.css
.drawer {
  width: 240px;
  flex-shrink: 0;
}

.drawerPaper {
  width: 240px;
  background-color: rgb(30, 35, 47);
}

.drawerTitle {
  color: white;
  font-size: 16px;
  font-weight: bold;
}

// CommonDrawer.tsx
import styles from './CommonDrawer.module.css';
import { Drawer, Text } from '@l-fin/ui-components';

function CommonDrawer() {
  return (
    <Drawer
      className={styles.drawer}
      styles={{ content: { backgroundColor: 'rgb(30, 35, 47)' } }}
    >
      <Text className={styles.drawerTitle}>Title</Text>
    </Drawer>
  );
}
```

### withStyles → styles prop

```jsx
// Before: MUI withStyles
import { withStyles } from '@material-ui/core/styles';
import TableCell from '@material-ui/core/TableCell';

const StyledTableCell = withStyles((theme) => ({
  head: {
    backgroundColor: 'rgb(30, 35, 47)',
    color: theme.palette.common.white,
    fontSize: '13px',
    textAlign: 'center',
  },
  body: {
    fontSize: '13px',
    textAlign: 'center',
  },
}))(TableCell);
```

```tsx
// After: DataTable (recommended) or Table.Td
import { DataTable } from '@l-fin/ui-components';

// DataTable uses its own style system
<DataTable
  columns={[
    { accessor: 'id', title: '번호', textAlign: 'center' },
    { accessor: 'name', title: '이름', textAlign: 'center' },
  ]}
  records={data}
/>

// Or basic Table usage
import { Table } from '@l-fin/ui-components';

<Table>
  <Table.Thead style={{ backgroundColor: 'rgb(30, 35, 47)' }}>
    <Table.Tr>
      <Table.Th style={{ color: 'white', textAlign: 'center' }}>번호</Table.Th>
    </Table.Tr>
  </Table.Thead>
</Table>
```

---

## Theme Color Reference

### Before: Hardcoded Colors

```jsx
const StyledComponent = styled.div`
  background-color: rgb(30, 35, 47);
  color: #1976d2;
  border: 1px solid rgb(60, 75, 100);
`;
```

### After: CSS Variables

```tsx
// CSS Variables usage
<Box
  style={{
    backgroundColor: 'var(--mantine-color-dark-7)',
    color: 'var(--mantine-color-blue-6)',
    border: '1px solid var(--mantine-color-gray-6)',
  }}
>

// Or style props
<Box bg="dark.7" c="blue.6">

// In CSS Modules
.container {
  background-color: var(--mantine-color-dark-7);
  color: var(--mantine-color-blue-6);
}
```

**Color Mapping**:
| Original Color        | Mantine Variable                   |
| --------------------- | ---------------------------------- |
| `rgb(30, 35, 47)`     | `var(--mantine-color-dark-7)`      |
| `rgb(60, 75, 100)`    | `var(--mantine-color-gray-6)`      |
| `#1976d2`             | `var(--mantine-color-blue-6)`      |
| `rgb(221, 243, 255)`  | `var(--mantine-color-blue-0)`      |
| `white`               | `var(--mantine-color-white)`       |

---

## GlobalStyle Conversion

### Before: styled-components GlobalStyle

```jsx
// global-styles.js
import { createGlobalStyle } from 'styled-components';

const GlobalStyle = createGlobalStyle`
  html, body {
    height: 100%;
    width: 100%;
  }

  body {
    font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
  }

  #app {
    background-color: #fafafa;
    min-height: 100%;
    min-width: 100%;
  }
`;

export default GlobalStyle;
```

### After: CSS File

```css
/* styles/global.css */
html, body {
  height: 100%;
  width: 100%;
}

body {
  font-family: var(--mantine-font-family);
}

#root {
  background-color: var(--mantine-color-gray-0);
  min-height: 100%;
  min-width: 100%;
}
```

```tsx
// main.tsx
import '@l-fin/ui-components/styles.css';
import './styles/global.css';
```

---

## Responsive Styles

### Before: styled-components media query

```jsx
const Container = styled.div`
  padding: 20px;

  @media (max-width: 768px) {
    padding: 10px;
  }
`;
```

### After: Mantine breakpoints

```tsx
// CSS Modules
.container {
  padding: 20px;

  @media (max-width: 768px) {
    padding: 10px;
  }
}

// Or useMediaQuery hook
import { useMediaQuery } from '@l-fin/ui-components';

function Component() {
  const isMobile = useMediaQuery('(max-width: 768px)');

  return (
    <Box p={isMobile ? 'sm' : 'lg'}>
      {/* ... */}
    </Box>
  );
}

// Or style props with responsive values (Grid, etc.)
<Grid>
  <Grid.Col span={{ base: 12, md: 6 }}>
    {/* ... */}
  </Grid.Col>
</Grid>
```

---

## Hover/Focus States

### Before: styled-components pseudo-selectors

```jsx
const Button = styled.button`
  background: transparent;

  :hover {
    background: blue;
    cursor: pointer;
  }

  :focus {
    outline: 2px solid blue;
  }
`;
```

### After: CSS Modules

```css
/* Button.module.css */
.button {
  background: transparent;
}

.button:hover {
  background: var(--mantine-color-blue-6);
  cursor: pointer;
}

.button:focus {
  outline: 2px solid var(--mantine-color-blue-6);
}
```

---

## Search Commands

```bash
# Find styled-components imports
Grep pattern="from ['\"]styled-components['\"]" glob="*.{js,jsx}" path="prev/"

# Find styled.xxx pattern
Grep pattern="styled\\." glob="*.{js,jsx}" path="prev/"

# Find makeStyles
Grep pattern="makeStyles" glob="*.{js,jsx}" path="prev/"

# Find withStyles
Grep pattern="withStyles" glob="*.{js,jsx}" path="prev/"

# Find GlobalStyle
Grep pattern="createGlobalStyle" glob="*.{js,jsx}" path="prev/"
```

---

## Removal Targets

Remove after migration:

1. **Packages**:
   - `styled-components`
   - `babel-plugin-styled-components`
   - `jest-styled-components`
   - `stylelint-processor-styled-components`
   - `stylelint-config-styled-components`

2. **Files**:
   - `global-styles.js`

```bash
pnpm remove styled-components babel-plugin-styled-components jest-styled-components
```

---

## Notes

1. **CSS Modules recommended**: Use CSS Modules for most cases
2. **Style Props**: Use Box style props for simple layout (`p`, `m`, `bg`, etc.)
3. **styles prop**: Use for styling component inner elements
4. **CSS Variables**: Reference theme colors via CSS Variables
5. **Remove !important**: Mantine components have fewer specificity issues
