# Chakra UI → @l-fin/ui-components

Migration guide from @chakra-ui/react to @l-fin/ui-components.

## Overview

| Item          | Value                              |
| ------------- | ---------------------------------- |
| Difficulty    | Medium                             |
| Automatable   | 50~60%                             |
| Main work     | Props name conversion + style props|

---

## Component Mapping

| Chakra UI     | @l-fin/ui-components    | Note                   |
| ------------- | ----------------------- | ---------------------- |
| `<Button>`    | `<Button>`              | colorScheme → color    |
| `<Input>`     | `<TextInput>`           | component name change  |
| `<Textarea>`  | `<Textarea>`            |                        |
| `<Select>`    | `<Select>`              |                        |
| `<Checkbox>`  | `<Checkbox>`            |                        |
| `<Switch>`    | `<Switch>`              |                        |
| `<Box>`       | `<Box>`                 |                        |
| `<Flex>`      | `<Flex>` or `<Group>`   |                        |
| `<Stack>`     | `<Stack>`               |                        |
| `<HStack>`    | `<Group>`               |                        |
| `<VStack>`    | `<Stack>`               |                        |
| `<Text>`      | `<Text>`                |                        |
| `<Heading>`   | `<Title>`               | component name change  |
| `<Modal>`     | `<Modal>`               | isOpen → opened        |
| `<Drawer>`    | `<Drawer>`              | isOpen → opened        |
| `<Menu>`      | `<Menu>`                |                        |
| `<Tabs>`      | `<Tabs>`                |                        |
| `<Accordion>` | `<Accordion>`           |                        |
| `<Alert>`     | `<Alert>`               |                        |
| `<Badge>`     | `<Badge>`               |                        |
| `<Avatar>`    | `<Avatar>`              |                        |
| `<Spinner>`   | `<Loader>`              |                        |
| `<Progress>`  | `<Progress>`            |                        |
| `<Skeleton>`  | `<Skeleton>`            |                        |
| `<Tooltip>`   | `<Tooltip>`             |                        |
| `<Popover>`   | `<Popover>`             |                        |
| `<Toast>`     | `notifications`         | @mantine/notifications |
| `<Card>`      | `<Card>`                |                        |
| `<Divider>`   | `<Divider>`             |                        |

---

## Props Conversion

### Button

```tsx
// Chakra UI
<Button colorScheme="blue" isLoading={loading} isDisabled={disabled}>
  Submit
</Button>

// @l-fin/ui-components
<Button color="blue" loading={loading} disabled={disabled}>
  Submit
</Button>
```

**Props mapping**:
| Chakra UI     | @l-fin/ui-components |
| ------------- | -------------------- |
| `colorScheme` | `color`              |
| `isLoading`   | `loading`            |
| `isDisabled`  | `disabled`           |
| `isOpen`      | `opened`             |
| `onOpen`      | - (use useDisclosure)|
| `onClose`     | `onClose`            |

### Modal

```tsx
// Chakra UI
import {
  Modal,
  ModalOverlay,
  ModalContent,
  ModalHeader,
  ModalBody,
  ModalCloseButton,
} from '@chakra-ui/react';

<Modal isOpen={isOpen} onClose={onClose}>
  <ModalOverlay />
  <ModalContent>
    <ModalHeader>제목</ModalHeader>
    <ModalCloseButton />
    <ModalBody>내용</ModalBody>
  </ModalContent>
</Modal>;

// @l-fin/ui-components
import {Modal} from '@l-fin/ui-components';

<Modal opened={isOpen} onClose={onClose} title="제목">
  내용
</Modal>;
```

### Input → TextInput

```tsx
// Chakra UI
<FormControl isInvalid={!!errors.email}>
  <FormLabel>Email</FormLabel>
  <Input type="email" {...register('email')} />
  <FormErrorMessage>{errors.email?.message}</FormErrorMessage>
</FormControl>

// @l-fin/ui-components
<TextInput
  label="Email"
  type="email"
  error={errors.email?.message}
  {...form.getInputProps('email')}
/>
```

---

## Style Props Handling

Chakra's style props are mostly replaced with className + Tailwind:

```tsx
// Chakra UI
<Box p={4} mt={2} bg="gray.100" borderRadius="md">

// @l-fin/ui-components + Tailwind
<Box className="p-4 mt-2 bg-gray-100 rounded-md">

// Or style prop
<Box style={{ padding: 16, marginTop: 8, backgroundColor: '#f5f5f5', borderRadius: 8 }}>
```

**Common mappings**:
| Chakra Style Prop | Tailwind Class |
| ----------------- | -------------- |
| `p={4}`           | `p-4`          |
| `m={2}`           | `m-2`          |
| `mt={4}`          | `mt-4`         |
| `gap={2}`         | `gap-2`        |
| `bg="gray.100"`   | `bg-gray-100`  |
| `borderRadius="md"`| `rounded-md`  |
| `w="full"`        | `w-full`       |
| `h="100vh"`       | `h-screen`     |

---

## useDisclosure Hook

```tsx
// Chakra UI
import {useDisclosure} from '@chakra-ui/react';

const {isOpen, onOpen, onClose} = useDisclosure();

// @l-fin/ui-components
import {useDisclosure} from '@l-fin/ui-components';

const [opened, {open, close, toggle}] = useDisclosure(false);
```

---

## Toast → notifications

```tsx
// Chakra UI
import {useToast} from '@chakra-ui/react';

const toast = useToast();
toast({
  title: '성공',
  description: '저장되었습니다',
  status: 'success',
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
# Find Chakra UI imports
Grep pattern="from ['\"]@chakra-ui/react['\"]" glob="*.{ts,tsx}" path="src/"

# Find isOpen/onOpen/onClose pattern
Grep pattern="isOpen|onOpen" glob="*.{ts,tsx}" path="src/"

# Find ChakraProvider
Grep pattern="ChakraProvider" glob="*.{ts,tsx}" path="src/"
```

---

## Removal Targets

Remove after migration:

1. **Packages**:
   - `@chakra-ui/react`
   - `@chakra-ui/icons`
   - `@emotion/react`
   - `@emotion/styled`
   - `framer-motion`

```bash
pnpm remove @chakra-ui/react @chakra-ui/icons @emotion/react @emotion/styled framer-motion
```

> **Composite components** (DataTable, FilterBuilder, AppLayout): See `/apply-composites` skill.

---

## Notes

1. **Style props**: Replace with Tailwind or style prop
2. **is* prefix**: `isOpen` → `opened`, `isLoading` → `loading`, etc.
3. **useDisclosure**: Different return format (object → array)
4. **Toast**: useToast → notifications.show
5. **Icons**: @chakra-ui/icons → @tabler/icons-react (separate migration)
