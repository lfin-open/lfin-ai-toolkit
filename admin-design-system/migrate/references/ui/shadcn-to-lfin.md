# shadcn/ui → @l-fin/ui-components

Migration guide from shadcn/ui to @l-fin/ui-components.

## Overview

| Item          | Value                            |
| ------------- | -------------------------------- |
| Difficulty    | Medium                           |
| Automatable   | 60~70%                           |
| Main work     | Component API mapping + cleanup  |

shadcn/ui uses Radix UI + Tailwind CSS combination, copying source to `components/ui/` folder.

---

## Identification

```
src/
└── components/
    └── ui/
        ├── button.tsx      ← shadcn/ui pattern
        ├── dialog.tsx
        ├── input.tsx
        └── ...
```

If `@radix-ui/*` packages exist in package.json and `components/ui/` folder exists, it's shadcn/ui.

---

## Component Mapping

### Basic Components

| shadcn/ui  | @l-fin/ui-components | Note                |
| ---------- | -------------------- | ------------------- |
| `Button`   | `Button`             | Similar variants    |
| `Input`    | `TextInput`          | Name change         |
| `Textarea` | `Textarea`           |                     |
| `Select`   | `Select`             | Similar API         |
| `Checkbox` | `Checkbox`           |                     |
| `Switch`   | `Switch`             |                     |
| `Slider`   | `Slider`             |                     |
| `Badge`    | `Badge`              |                     |
| `Card`     | `Card` / `Paper`     |                     |
| `Table`    | `Table`              |                     |

### Overlay Components

| shadcn/ui      | @l-fin/ui-components    | Note              |
| -------------- | ----------------------- | ----------------- |
| `Dialog`       | `Modal`                 | `open` → `opened` |
| `AlertDialog`  | `Modal` + `Button`      | Compose manually  |
| `Sheet`        | `Drawer`                |                   |
| `Popover`      | `Popover`               |                   |
| `Tooltip`      | `Tooltip`               |                   |
| `DropdownMenu` | `Menu`                  |                   |
| `ContextMenu`  | `Menu` + useContextMenu |                   |
| `HoverCard`    | `HoverCard`             |                   |

### Navigation/Layout

| shadcn/ui        | @l-fin/ui-components          | Note |
| ---------------- | ----------------------------- | ---- |
| `Tabs`           | `Tabs`                        |      |
| `Accordion`      | `Accordion`                   |      |
| `Sidebar`        | `AppShell.Navbar` or custom   |      |
| `Breadcrumb`     | `Breadcrumbs`                 |      |
| `Pagination`     | `Pagination`                  |      |
| `NavigationMenu` | `NavLink` / custom            |      |
| `Collapsible`    | `Collapse`                    |      |

### Misc

| shadcn/ui      | @l-fin/ui-components | Note                   |
| -------------- | -------------------- | ---------------------- |
| `Avatar`       | `Avatar`             |                        |
| `Progress`     | `Progress`           |                        |
| `Skeleton`     | `Skeleton`           |                        |
| `Separator`    | `Divider`            |                        |
| `ScrollArea`   | `ScrollArea`         |                        |
| `Toast/Sonner` | `notifications`      | @mantine/notifications |
| `Calendar`     | `DatePicker`         | @mantine/dates         |
| `RadioGroup`   | `Radio.Group`        |                        |
| `Label`        | `Input.Label`        | Built into Input       |
| `Form`         | -                    | Use @mantine/form      |
| `AspectRatio`  | `AspectRatio`        |                        |

---

## Props Conversion

### Dialog → Modal

```tsx
// shadcn/ui
import {Dialog, DialogContent, DialogHeader, DialogTitle} from '@/components/ui/dialog';

<Dialog open={isOpen} onOpenChange={setIsOpen}>
  <DialogContent>
    <DialogHeader>
      <DialogTitle>제목</DialogTitle>
    </DialogHeader>
    Content
  </DialogContent>
</Dialog>;

// @l-fin/ui-components
import {Modal} from '@l-fin/ui-components';

<Modal opened={isOpen} onClose={() => setIsOpen(false)} title="제목">
  Content
</Modal>;
```

### Button

```tsx
// shadcn/ui
<Button variant="default" size="lg" disabled={loading}>
  Submit
</Button>

// @l-fin/ui-components
<Button variant="filled" size="lg" disabled={loading}>
  Submit
</Button>
```

**variant mapping**:
| shadcn/ui     | @l-fin/ui-components         |
| ------------- | ---------------------------- |
| `default`     | `filled`                     |
| `destructive` | `filled` + `color="red"`     |
| `outline`     | `outline`                    |
| `secondary`   | `light`                      |
| `ghost`       | `subtle`                     |
| `link`        | `transparent`                |

### Input → TextInput

```tsx
// shadcn/ui
import { Input } from "@/components/ui/input"
import { Label } from "@/components/ui/label"

<Label htmlFor="email">Email</Label>
<Input id="email" type="email" placeholder="Email" />

// @l-fin/ui-components
import { TextInput } from '@l-fin/ui-components';

<TextInput label="Email" type="email" placeholder="Email" />
```

### Select

```tsx
// shadcn/ui
import {
  Select,
  SelectContent,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from '@/components/ui/select';

<Select value={value} onValueChange={setValue}>
  <SelectTrigger>
    <SelectValue placeholder="선택하세요" />
  </SelectTrigger>
  <SelectContent>
    <SelectItem value="1">옵션 1</SelectItem>
    <SelectItem value="2">옵션 2</SelectItem>
  </SelectContent>
</Select>;

// @l-fin/ui-components
import {Select} from '@l-fin/ui-components';

<Select
  value={value}
  onChange={setValue}
  placeholder="선택하세요"
  data={[
    {value: '1', label: '옵션 1'},
    {value: '2', label: '옵션 2'},
  ]}
/>;
```

---

## Props Differences

| shadcn/ui               | @l-fin/ui-components   | Description                    |
| ----------------------- | ---------------------- | ------------------------------ |
| `open`                  | `opened`               | Modal/Drawer/Popover open state|
| `onOpenChange`          | `onClose`              | Modal close handler            |
| `variant="destructive"` | `color="red"`          | Danger action indicator        |
| `size: sm/default/lg`   | `size: xs/sm/md/lg/xl` | Size options                   |

---

## Migration Strategy

### 1. Delete components/ui/ Folder

Delete after replacing shadcn/ui components with @l-fin/ui-components.

### 2. Change Import Paths

```tsx
// Before
import {Button} from '@/components/ui/button';
import {Dialog, DialogContent} from '@/components/ui/dialog';

// After
import {Button, Modal} from '@l-fin/ui-components';
```

### 3. Handle cn() Utility

Tailwind classes can be kept via className:

```tsx
// Before
import { cn } from "@/lib/utils"
<Button className={cn("custom-class", className)}>

// After - className can be kept
<Button className="custom-class">
```

---

## Search Commands

Find target files:

```bash
# Find shadcn/ui component files
Glob pattern="src/components/ui/*.tsx"

# Find shadcn/ui imports
Grep pattern="from ['\"]@/components/ui/" glob="*.{ts,tsx}" path="src/"

# Find @radix-ui imports
Grep pattern="from ['\"]@radix-ui/" glob="*.{ts,tsx}" path="src/"

# Find cn() utility usage
Grep pattern="cn\\(" glob="*.{ts,tsx}" path="src/"
```

---

## Removal Targets

Remove after migration:

1. **Folders**: `src/components/ui/`
2. **Files**: `src/lib/utils.ts` (if only contains cn utility)
3. **Packages**:
   - `@radix-ui/*` (all @radix-ui packages)
   - `class-variance-authority`
   - `clsx`
   - `tailwind-merge` (if not used elsewhere)

```bash
# Package removal example (pnpm)
pnpm remove @radix-ui/react-dialog @radix-ui/react-dropdown-menu @radix-ui/react-select class-variance-authority
```

---

## Extra Features

LFin-specific features available after migration:

| Component       | Extra Props                                                              |
| --------------- | ------------------------------------------------------------------------ |
| `Button`        | `preventRapidClick` (rapid click prevention), `autoLoading` (auto loading for async onClick) |
| `ActionIcon`    | `preventRapidClick`, `autoLoading`                                       |
| `PasswordInput` | `showStrength`, `showCapsLockWarning`, `showGenerator`                   |
| `Image`         | `withPreview`, `showDownloadButton`, `zoomable`, `fallbackText`          |

> **Composite components** (DataTable, FilterBuilder, AppLayout): See `/apply-composites` skill.

---

## Notes

1. **Style conflicts**: Keep Tailwind CSS but remove shadcn custom styles
2. **Compound Components**: shadcn `Dialog.Content` pattern → Modal props
3. **Form handling**: shadcn Form → separate migration to @mantine/form (see references/form/rhf-to-mantine-form.md)
4. **Icons**: If using lucide-react, separate migration to tabler required
