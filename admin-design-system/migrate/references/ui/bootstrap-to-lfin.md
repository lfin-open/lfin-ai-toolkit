# Bootstrap → @l-fin/ui-components

Migration guide from react-bootstrap to @l-fin/ui-components.

## Overview

| Item          | Value                                |
| ------------- | ------------------------------------ |
| Difficulty    | Medium                               |
| Automatable   | 50~60%                               |
| Main work     | Component API mapping + Grid system  |

---

## Component Mapping

| react-bootstrap                 | @l-fin/ui-components         | Note                   |
| ------------------------------- | ---------------------------- | ---------------------- |
| `<Button variant="primary">`    | `<Button variant="filled">`  | variant name change    |
| `<Button variant="secondary">`  | `<Button variant="outline">` |                        |
| `<Button variant="outline-*">`  | `<Button variant="outline">` |                        |
| `<Form.Control>`                | `<TextInput>`                | structure change       |
| `<Form.Select>`                 | `<Select>`                   |                        |
| `<Form.Check type="checkbox">`  | `<Checkbox>`                 |                        |
| `<Form.Check type="switch">`    | `<Switch>`                   |                        |
| `<Modal>`                       | `<Modal>`                    | show → opened          |
| `<Offcanvas>`                   | `<Drawer>`                   |                        |
| `<Dropdown>`                    | `<Menu>`                     |                        |
| `<Nav>`, `<Navbar>`             | `<AppShell>`                 | layout structure change|
| `<Tabs>`                        | `<Tabs>`                     |                        |
| `<Accordion>`                   | `<Accordion>`                |                        |
| `<Alert>`                       | `<Alert>`                    |                        |
| `<Badge>`                       | `<Badge>`                    |                        |
| `<Spinner>`                     | `<Loader>`                   | component name change  |
| `<ProgressBar>`                 | `<Progress>`                 |                        |
| `<Card>`                        | `<Card>`                     |                        |
| `<Table>`                       | `<Table>`                    |                        |
| `<Tooltip>`                     | `<Tooltip>`                  |                        |
| `<Popover>`                     | `<Popover>`                  |                        |
| `<Toast>`                       | `notifications`              | @mantine/notifications |
| `<Container>`, `<Row>`, `<Col>` | `<Grid>`, `<Grid.Col>`       | Grid system            |
| `<Stack>`                       | `<Stack>` or `<Group>`       |                        |

---

## Props Conversion

### Button

```tsx
// react-bootstrap
<Button variant="primary" disabled={loading}>
  Submit
</Button>

// @l-fin/ui-components
<Button variant="filled" disabled={loading}>
  Submit
</Button>
```

**variant mapping**:
| react-bootstrap   | @l-fin/ui-components         |
| ----------------- | ---------------------------- |
| `primary`         | `filled`                     |
| `secondary`       | `light`                      |
| `outline-primary` | `outline`                    |
| `link`            | `transparent`                |
| `danger`          | `filled` + `color="red"`     |

### Modal

```tsx
// react-bootstrap
<Modal show={isOpen} onHide={onClose}>
  <Modal.Header closeButton>
    <Modal.Title>제목</Modal.Title>
  </Modal.Header>
  <Modal.Body>내용</Modal.Body>
  <Modal.Footer>
    <Button variant="secondary" onClick={onClose}>취소</Button>
    <Button variant="primary" onClick={onConfirm}>확인</Button>
  </Modal.Footer>
</Modal>

// @l-fin/ui-components
<Modal opened={isOpen} onClose={onClose} title="제목">
  내용
  <Group justify="flex-end" mt="md">
    <Button variant="outline" onClick={onClose}>취소</Button>
    <Button onClick={onConfirm}>확인</Button>
  </Group>
</Modal>
```

### Form.Control → TextInput

```tsx
// react-bootstrap
<Form.Group>
  <Form.Label>Email</Form.Label>
  <Form.Control
    type="email"
    placeholder="Enter email"
    isInvalid={!!errors.email}
  />
  <Form.Control.Feedback type="invalid">
    {errors.email?.message}
  </Form.Control.Feedback>
</Form.Group>

// @l-fin/ui-components
<TextInput
  label="Email"
  type="email"
  placeholder="Enter email"
  error={errors.email?.message}
  {...form.getInputProps('email')}
/>
```

---

## Grid System Conversion

### Container/Row/Col → Grid

```tsx
// react-bootstrap
<Container>
  <Row>
    <Col md={6}>Left</Col>
    <Col md={6}>Right</Col>
  </Row>
</Container>

// @l-fin/ui-components (Grid)
<Grid>
  <Grid.Col span={6}>Left</Grid.Col>
  <Grid.Col span={6}>Right</Grid.Col>
</Grid>

// Or Flex/Group
<Group grow>
  <Box>Left</Box>
  <Box>Right</Box>
</Group>
```

**breakpoint mapping**:
| react-bootstrap | @l-fin/ui-components |
| --------------- | -------------------- |
| `xs`            | `base`               |
| `sm`            | `xs`                 |
| `md`            | `sm`                 |
| `lg`            | `md`                 |
| `xl`            | `lg`                 |
| `xxl`           | `xl`                 |

```tsx
// react-bootstrap
<Col xs={12} md={6} lg={4}>

// @l-fin/ui-components
<Grid.Col span={{ base: 12, sm: 6, md: 4 }}>
```

---

## Navbar → AppShell

```tsx
// react-bootstrap
<Navbar bg="dark" variant="dark">
  <Container>
    <Navbar.Brand href="/">Logo</Navbar.Brand>
    <Nav>
      <Nav.Link href="/home">Home</Nav.Link>
      <Nav.Link href="/about">About</Nav.Link>
    </Nav>
  </Container>
</Navbar>

// @l-fin/ui-components
<AppShell
  header={{ height: 60 }}
  padding="md"
>
  <AppShell.Header>
    <Group h="100%" px="md">
      <Anchor href="/">Logo</Anchor>
      <Group>
        <Anchor href="/home">Home</Anchor>
        <Anchor href="/about">About</Anchor>
      </Group>
    </Group>
  </AppShell.Header>
  <AppShell.Main>
    {/* content */}
  </AppShell.Main>
</AppShell>
```

---

## Search Commands

```bash
# Find react-bootstrap imports
Grep pattern="from ['\"]react-bootstrap['\"]" glob="*.{ts,tsx}" path="src/"

# Find show prop (Modal)
Grep pattern="show=" glob="*.{ts,tsx}" path="src/"

# Find Grid usage (Container, Row, Col)
Grep pattern="<(Container|Row|Col)" glob="*.{ts,tsx}" path="src/"
```

---

## Removal Targets

Remove after migration:

1. **Packages**:
   - `react-bootstrap`
   - `bootstrap` (CSS)

2. **CSS imports**:
   - `import 'bootstrap/dist/css/bootstrap.min.css'`

```bash
pnpm remove react-bootstrap bootstrap
```

> **Composite components** (DataTable, FilterBuilder, AppLayout): See `/apply-composites` skill.

---

## Notes

1. **show → opened**: Modal, Offcanvas
2. **onHide → onClose**: Modal
3. **variant names**: primary → filled, etc.
4. **Grid breakpoints**: Names differ (md → sm, etc.)
5. **Bootstrap CSS removal**: Remove bootstrap CSS import to prevent conflicts
6. **Form structure**: Form.Group/Form.Control → single component (TextInput, etc.)
