# Ant Design → @l-fin/ui-components

Migration guide from antd to @l-fin/ui-components.

## Overview

| Item          | Value                                |
| ------------- | ------------------------------------ |
| Difficulty    | High                                 |
| Automatable   | 30~40%                               |
| Main work     | Component API mapping + Form system  |

---

## Component Mapping

| Ant Design                | @l-fin/ui-components             | Note                                                         |
| ------------------------- | -------------------------------- | ------------------------------------------------------------ |
| `<Button type="primary">` | `<Button variant="filled">`      | type → variant                                               |
| `<Button type="default">` | `<Button variant="outline">`     |                                                              |
| `<Button type="text">`    | `<Button variant="subtle">`      |                                                              |
| `<Button type="link">`    | `<Button variant="transparent">` |                                                              |
| `<Input>`                 | `<TextInput>`                    |                                                              |
| `<Input.TextArea>`        | `<Textarea>`                     |                                                              |
| `<Input.Password>`        | `<PasswordInput>`                |                                                              |
| `<InputNumber>`           | `<NumberInput>`                  |                                                              |
| `<Select>`                | `<Select>`                       | Check options structure                                      |
| `<Checkbox>`              | `<Checkbox>`                     |                                                              |
| `<Switch>`                | `<Switch>`                       |                                                              |
| `<Slider>`                | `<Slider>`                       |                                                              |
| `<Table>`                 | `<Table>`                        | Different columns structure (see `/apply-composites`)        |
| `<Modal>`                 | `<Modal>`                        | visible → opened                                             |
| `<Drawer>`                | `<Drawer>`                       | visible → opened                                             |
| `<Menu>`                  | `<Menu>`                         |                                                              |
| `<Dropdown>`              | `<Menu>`                         |                                                              |
| `<Tabs>`                  | `<Tabs>`                         |                                                              |
| `<Collapse>`              | `<Accordion>`                    |                                                              |
| `<Alert>`                 | `<Alert>`                        |                                                              |
| `<Badge>`                 | `<Badge>`                        |                                                              |
| `<Avatar>`                | `<Avatar>`                       |                                                              |
| `<Spin>`                  | `<Loader>`                       |                                                              |
| `<Progress>`              | `<Progress>`                     |                                                              |
| `<Skeleton>`              | `<Skeleton>`                     |                                                              |
| `<Tooltip>`               | `<Tooltip>`                      |                                                              |
| `<Popover>`               | `<Popover>`                      |                                                              |
| `<message>`               | `notifications`                  | @mantine/notifications                                       |
| `<notification>`          | `notifications`                  | @mantine/notifications                                       |
| `<Card>`                  | `<Card>`                         |                                                              |
| `<Divider>`               | `<Divider>`                      |                                                              |
| `<Space>`                 | `<Group>`                        |                                                              |
| `<Row>`, `<Col>`          | `<Grid>`, `<Grid.Col>`           |                                                              |
| `<Layout>`                | `<AppShell>`                     |                                                              |
| `<Form>`                  | Use @mantine/form                | Form system change                                           |
| `<DatePicker>`            | `<DatePicker>`                   |                                                              |

---

## Props Conversion

### Button

```tsx
// Ant Design
<Button type="primary" loading={loading} disabled={disabled}>
  Submit
</Button>

// @l-fin/ui-components
<Button variant="filled" loading={loading} disabled={disabled}>
  Submit
</Button>
```

**type → variant mapping**:
| Ant Design | @l-fin/ui-components         |
| ---------- | ---------------------------- |
| `primary`  | `filled`                     |
| `default`  | `outline`                    |
| `dashed`   | `outline` (dashed style separate) |
| `text`     | `subtle`                     |
| `link`     | `transparent`                |

### Modal

```tsx
// Ant Design
<Modal
  title="제목"
  visible={visible}
  onOk={handleOk}
  onCancel={handleCancel}
>
  내용
</Modal>

// @l-fin/ui-components
<Modal
  title="제목"
  opened={visible}
  onClose={handleCancel}
>
  내용
  <Group justify="flex-end" mt="md">
    <Button variant="outline" onClick={handleCancel}>취소</Button>
    <Button onClick={handleOk}>확인</Button>
  </Group>
</Modal>
```

### Table

```tsx
// Ant Design
const columns = [
  { title: '이름', dataIndex: 'name', key: 'name' },
  { title: '나이', dataIndex: 'age', key: 'age' },
];

<Table dataSource={data} columns={columns} />

// @l-fin/ui-components (basic Table)
<Table>
  <Table.Thead>
    <Table.Tr>
      <Table.Th>이름</Table.Th>
      <Table.Th>나이</Table.Th>
    </Table.Tr>
  </Table.Thead>
  <Table.Tbody>
    {data.map((item) => (
      <Table.Tr key={item.key}>
        <Table.Td>{item.name}</Table.Td>
        <Table.Td>{item.age}</Table.Td>
      </Table.Tr>
    ))}
  </Table.Tbody>
</Table>

// For complex tables (sorting, pagination, filtering, etc.), see `/apply-composites` skill.
```

### Select

```tsx
// Ant Design
<Select
  value={value}
  onChange={setValue}
  options={[
    { value: '1', label: '옵션 1' },
    { value: '2', label: '옵션 2' },
  ]}
/>

// @l-fin/ui-components
<Select
  value={value}
  onChange={setValue}
  data={[
    { value: '1', label: '옵션 1' },
    { value: '2', label: '옵션 2' },
  ]}
/>
```

**Difference**: `options` → `data`

---

## Form System Conversion

Ant Design Form → @mantine/form:

```tsx
// Ant Design
import {Form, Input, Button} from 'antd';

const [form] = Form.useForm();

<Form form={form} onFinish={onSubmit}>
  <Form.Item name="email" label="Email" rules={[{required: true, message: '이메일을 입력하세요'}]}>
    <Input />
  </Form.Item>
  <Button type="primary" htmlType="submit">
    Submit
  </Button>
</Form>;

// @l-fin/ui-components + @mantine/form
import {useForm} from '@mantine/form';
import {TextInput, Button} from '@l-fin/ui-components';

const form = useForm({
  initialValues: {email: ''},
  validate: {
    email: (value) => (!value ? '이메일을 입력하세요' : null),
  },
});

<form onSubmit={form.onSubmit(onSubmit)}>
  <TextInput label="Email" {...form.getInputProps('email')} />
  <Button type="submit">Submit</Button>
</form>;
```

---

## message/notification → notifications

```tsx
// Ant Design
import {message, notification} from 'antd';

message.success('저장되었습니다');
notification.success({
  message: '성공',
  description: '저장되었습니다',
});

// @l-fin/ui-components
import {notifications} from '@mantine/notifications';

notifications.show({
  title: '성공',
  message: '저장되었습니다',
  color: 'green',
});
```

---

## Search Commands

```bash
# Find antd imports
Grep pattern="from ['\"]antd['\"]" glob="*.{ts,tsx}" path="src/"

# Find visible prop (Modal/Drawer)
Grep pattern="visible=" glob="*.{ts,tsx}" path="src/"

# Find Form.Item
Grep pattern="Form\\.Item" glob="*.{ts,tsx}" path="src/"
```

---

## Removal Targets

Remove after migration:

1. **Packages**:
   - `antd`
   - `@ant-design/icons`

```bash
pnpm remove antd @ant-design/icons
```

> **Composite components** (DataTable, FilterBuilder, AppLayout): See `/apply-composites` skill.

---

## Notes

1. **visible → opened**: Modal, Drawer, etc.
2. **type → variant**: Button
3. **options → data**: Select
4. **Form system**: Completely different, requires @mantine/form learning
5. **Table columns**: Different structure, see `/apply-composites` skill for complex tables
6. **Icons**: @ant-design/icons → @tabler/icons-react (separate migration)
