# MUI (Material UI) → @l-fin/ui-components

Migration guide from @mui/material (v5) or @material-ui/core (v4) to @l-fin/ui-components.

## Overview

| Item          | Value                                     |
| ------------- | ----------------------------------------- |
| Difficulty    | High                                      |
| Automatable   | 30~40%                                    |
| Main work     | Component API mapping + makeStyles/sx conversion |

---

## MUI v4 vs v5 Import Differences

```js
// MUI v4 (@material-ui/core)
import Button from '@material-ui/core/Button';
import { makeStyles, withStyles } from '@material-ui/core/styles';
import { Table, TableBody, TableCell } from '@material-ui/core';

// MUI v5 (@mui/material)
import Button from '@mui/material/Button';
import { styled } from '@mui/material/styles';
import { Table, TableBody, TableCell } from '@mui/material';
```

---

## Component Mapping

| MUI                            | @l-fin/ui-components         | Note                           |
| ------------------------------ | ---------------------------- | ------------------------------ |
| `<Button variant="contained">` | `<Button variant="filled">`  | variant name change            |
| `<Button variant="outlined">`  | `<Button variant="outline">` | variant name change            |
| `<Button variant="text">`      | `<Button variant="subtle">`  |                                |
| `<TextField>`                  | `<TextInput>`                | component name change          |
| `<Box>`                        | `<Box>`                      | sx prop → style or className   |
| `<Stack>`                      | `<Stack>`                    | spacing unit differs           |
| `<Typography>`                 | `<Text>` / `<Title>`         | component name change          |
| `<Modal>`                      | `<Modal>`                    | open → opened                  |
| `<Dialog>`                     | `<Modal>`                    | component merge                |
| `<Drawer>`                     | `<Drawer>`                   |                                |
| `<Menu>`                       | `<Menu>`                     |                                |
| `<Select>`                     | `<Select>`                   |                                |
| `<Checkbox>`                   | `<Checkbox>`                 |                                |
| `<Switch>`                     | `<Switch>`                   |                                |
| `<Slider>`                     | `<Slider>`                   |                                |
| `<Table>`                      | `<Table>`                    |                                |
| `<Tabs>`                       | `<Tabs>`                     |                                |
| `<Accordion>`                  | `<Accordion>`                |                                |
| `<Alert>`                      | `<Alert>`                    |                                |
| `<Badge>`                      | `<Badge>`                    |                                |
| `<Avatar>`                     | `<Avatar>`                   |                                |
| `<CircularProgress>`           | `<Loader>`                   |                                |
| `<LinearProgress>`             | `<Progress>`                 |                                |
| `<Skeleton>`                   | `<Skeleton>`                 |                                |
| `<Tooltip>`                    | `<Tooltip>`                  |                                |
| `<Popover>`                    | `<Popover>`                  |                                |
| `<Snackbar>`                   | `notifications`              | @mantine/notifications         |
| `<AppBar>`                     | `AppShell.Header`            |                                |
| `<Paper>`                      | `Paper`                      |                                |
| `<Card>`                       | `Card`                       |                                |
| `<Divider>`                    | `Divider`                    |                                |
| `<Grid>`                       | `Grid`                       | or Flex/Group                  |

---

## Props Conversion

### Button

```tsx
// MUI
<Button variant="contained" color="primary" disabled={loading}>
  Submit
</Button>

// @l-fin/ui-components
<Button variant="filled" color="blue" disabled={loading}>
  Submit
</Button>
```

**color mapping**:
| MUI         | @l-fin/ui-components          |
| ----------- | ----------------------------- |
| `primary`   | `blue` or default (primaryColor) |
| `secondary` | `gray`                        |
| `error`     | `red`                         |
| `warning`   | `yellow`                      |
| `success`   | `green`                       |

### TextField → TextInput

```tsx
// MUI
<TextField
  label="Email"
  variant="outlined"
  error={!!errors.email}
  helperText={errors.email?.message}
  {...register('email')}
/>

// @l-fin/ui-components
<TextInput
  label="Email"
  error={errors.email?.message}
  {...form.getInputProps('email')}
/>
```

### Modal/Dialog

```tsx
// MUI
<Dialog open={open} onClose={handleClose}>
  <DialogTitle>제목</DialogTitle>
  <DialogContent>내용</DialogContent>
  <DialogActions>
    <Button onClick={handleClose}>취소</Button>
    <Button onClick={handleConfirm}>확인</Button>
  </DialogActions>
</Dialog>

// @l-fin/ui-components
<Modal
  opened={open}
  onClose={handleClose}
  title="제목"
>
  내용
  <Group justify="flex-end" mt="md">
    <Button variant="outline" onClick={handleClose}>취소</Button>
    <Button onClick={handleConfirm}>확인</Button>
  </Group>
</Modal>
```

---

## sx prop Handling

MUI's `sx` prop is not supported. Alternatives:

### 1. className + Tailwind CSS

```tsx
// MUI
<Box sx={{ display: 'flex', gap: 2, p: 2 }}>

// @l-fin/ui-components + Tailwind
<Box className="flex gap-4 p-4">
```

### 2. style prop

```tsx
// MUI
<Box sx={{ backgroundColor: 'primary.main', borderRadius: 2 }}>

// @l-fin/ui-components
<Box style={{ backgroundColor: 'var(--mantine-color-blue-6)', borderRadius: 8 }}>
```

### 3. Mantine styles prop

```tsx
// @l-fin/ui-components
<Button styles={{ root: { padding: '12px 24px' } }}>
```

---

## Theme Conversion

```tsx
// MUI
import { createTheme, ThemeProvider } from '@mui/material';

const theme = createTheme({
  palette: {
    primary: { main: '#1976d2' },
  },
});

<ThemeProvider theme={theme}>

// @l-fin/ui-components
import { ThemeProvider, createAppTheme } from '@l-fin/ui-components';

const theme = createAppTheme({
  mantine: {
    primaryColor: 'blue',
  },
});

<ThemeProvider theme={theme}>
```

---

## Search Commands

```bash
# Find MUI v5 imports
Grep pattern="from ['\"]@mui/material" glob="*.{ts,tsx}" path="src/"

# Find MUI v4 imports
Grep pattern="from ['\"]@material-ui" glob="*.{js,jsx}" path="prev/"

# Find makeStyles usage (v4)
Grep pattern="makeStyles" glob="*.{js,jsx}" path="prev/"

# Find withStyles usage (v4)
Grep pattern="withStyles" glob="*.{js,jsx}" path="prev/"

# Find sx prop usage (v5)
Grep pattern="sx=\\{" glob="*.{ts,tsx}" path="src/"

# Find Table component
Grep pattern="<Table" glob="*.{js,jsx}" path="prev/"

# Find Pickers
Grep pattern="@material-ui/pickers" glob="*.{js,jsx}" path="prev/"
```

---

## Removal Targets

Remove after migration:

1. **Packages**:
   - `@mui/material`
   - `@mui/icons-material`
   - `@mui/system`
   - `@emotion/react`
   - `@emotion/styled`

```bash
pnpm remove @mui/material @mui/icons-material @mui/system @emotion/react @emotion/styled
```

> **Composite components** (DataTable, FilterBuilder, AppLayout): See `/apply-composites` skill.

---

## MUI v4 makeStyles/withStyles Conversion

### makeStyles → CSS Modules

```jsx
// Before: MUI v4 makeStyles
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
  title: {
    color: 'white',
    fontSize: '16px !important',
    fontWeight: 'bold',
  },
});

function CommonDrawer() {
  const classes = useStyles();
  return (
    <Drawer className={classes.drawer}>
      <Typography className={classes.title}>Title</Typography>
    </Drawer>
  );
}
```

```css
/* After: CommonDrawer.module.css */
.drawer {
  width: 240px;
  flex-shrink: 0;
}

.drawerPaper {
  width: 240px;
  background-color: rgb(30, 35, 47);
}

.title {
  color: white;
  font-size: 16px;
  font-weight: bold;
}
```

```tsx
// After: CommonDrawer.tsx
import styles from './CommonDrawer.module.css';
import { Drawer, Text } from '@l-fin/ui-components';

function CommonDrawer() {
  return (
    <Drawer className={styles.drawer}>
      <Text className={styles.title}>Title</Text>
    </Drawer>
  );
}
```

### withStyles → styles prop or CSS Modules

```jsx
// Before: MUI v4 withStyles
import { withStyles } from '@material-ui/core/styles';
import TableCell from '@material-ui/core/TableCell';
import TableRow from '@material-ui/core/TableRow';

const StyledTableCell = withStyles((theme) => ({
  head: {
    backgroundColor: 'rgb(30, 35, 47)',
    color: theme.palette.common.white,
    fontSize: '13px',
    textAlign: 'center',
    padding: '8px 4px !important',
  },
  body: {
    fontSize: '13px',
    textAlign: 'center',
  },
}))(TableCell);

const StyledTableRow = withStyles((theme) => ({
  root: {
    '&:nth-of-type(odd)': {
      backgroundColor: theme.palette.action.hover,
    },
    '&:hover': {
      backgroundColor: 'rgb(221,243,255)',
    },
  },
}))(TableRow);
```

```tsx
// After: DataTable usage (recommended)
import { DataTable } from '@l-fin/ui-components';

<DataTable
  columns={[
    { accessor: 'id', title: '번호', textAlign: 'center' },
    { accessor: 'requestId', title: 'Request ID', textAlign: 'center' },
  ]}
  records={data}
  striped
  highlightOnHover
/>
```

---

## MUI Table → DataTable Conversion

### Before: MUI Table (Complex Setup)

```jsx
import Table from '@material-ui/core/Table';
import TableBody from '@material-ui/core/TableBody';
import TableCell from '@material-ui/core/TableCell';
import TableContainer from '@material-ui/core/TableContainer';
import TableHead from '@material-ui/core/TableHead';
import TableRow from '@material-ui/core/TableRow';
import { TablePagination, Checkbox } from '@material-ui/core';

<TableContainer>
  <Table size="small">
    <TableHead>
      <TableRow>
        <StyledTableCell>번호</StyledTableCell>
        <StyledTableCell>Request ID</StyledTableCell>
        <StyledTableCell>
          <Checkbox checked={allChecked} onChange={handleCheckAll} />
        </StyledTableCell>
      </TableRow>
    </TableHead>
    <TableBody>
      {data.map((row, key) => (
        <StyledTableRow key={row.id}>
          <StyledTableCell>{key + 1}</StyledTableCell>
          <StyledTableCell onClick={() => onClickRow(row)}>{row.requestId}</StyledTableCell>
          <StyledTableCell>
            <Checkbox checked={selected.includes(row.id)} onChange={() => handleCheck(row.id)} />
          </StyledTableCell>
        </StyledTableRow>
      ))}
    </TableBody>
  </Table>
</TableContainer>
<TablePagination
  count={total}
  page={page}
  rowsPerPage={size}
  onChangePage={handleChangePage}
  onChangeRowsPerPage={handleChangeRowsPerPage}
/>
```

### After: DataTable (Concise)

```tsx
import { DataTable } from '@l-fin/ui-components';

<DataTable
  columns={[
    { accessor: 'index', title: '번호', render: (_, index) => index + 1 },
    { accessor: 'requestId', title: 'Request ID' },
  ]}
  records={data}
  totalRecords={total}
  page={page}
  onPageChange={setPage}
  recordsPerPage={size}
  onRecordsPerPageChange={setSize}
  recordsPerPageOptions={[10, 50, 100]}
  selectedRecords={selected}
  onSelectedRecordsChange={setSelected}
  onRowClick={({ record }) => onClickRow(record)}
  striped
  highlightOnHover
/>
```

---

## MUI v4 Pickers → DatePicker

```jsx
// Before: @material-ui/pickers
import { DatePicker, MuiPickersUtilsProvider } from '@material-ui/pickers';
import MomentUtils from '@date-io/moment';

<MuiPickersUtilsProvider utils={MomentUtils}>
  <DatePicker
    label="시작일"
    value={fromDate}
    onChange={setFromDate}
    format="YYYY-MM-DD"
  />
</MuiPickersUtilsProvider>
```

```tsx
// After: @l-fin/ui-components DatePicker
import { DatePicker } from '@l-fin/ui-components';

<DatePicker
  pickerType="date"
  label="시작일"
  value={fromDate}
  onChange={setFromDate}
/>
```

---

## Removal Targets (MUI v4)

Remove after migration:

1. **Packages (v4)**:
   - `@material-ui/core`
   - `@material-ui/icons`
   - `@material-ui/styles`
   - `@material-ui/pickers`
   - `@date-io/moment`

```bash
pnpm remove @material-ui/core @material-ui/icons @material-ui/styles @material-ui/pickers @date-io/moment
```

---

## Notes

1. **sx prop (v5) / makeStyles (v4)**: Replace with CSS Modules or styles prop
2. **spacing units**: MUI uses 8px base, Mantine uses rem base
3. **theme.palette**: Convert to Mantine color system
4. **Icons**: @material-ui/icons → @tabler/icons-react (separate migration)
5. **Form handling**: MUI Form → @mantine/form (separate migration)
6. **Table**: Use DataTable composite component for complex tables
7. **Pickers**: @material-ui/pickers → DatePicker component
