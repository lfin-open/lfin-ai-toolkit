# Custom Components → @l-fin/ui-components

Migration guide from custom/in-house components to @l-fin/ui-components.

## Overview

| Item          | Value                                |
| ------------- | ------------------------------------ |
| Difficulty    | Very High                            |
| Automatable   | 10~30%                               |
| Main work     | Analysis + 1:1 mapping + manual work |

Custom components vary by project, so standard mapping is not possible. Analysis-based approach required.

---

## Phase 1: Component Inventory

### 1-1. Extract Custom Component List

Search component directories with Glob:

- `src/**/components/**/*.tsx`
- `src/**/ui/**/*.tsx`
- `src/**/common/**/*.tsx`

### 1-2. Categorize Components

| Category        | Examples             | Migration Method                                          |
| --------------- | -------------------- | --------------------------------------------------------- |
| **Basic UI**    | Button, Input, Modal | Replace with @l-fin/ui-components                         |
| **Composite**   | SearchForm, Table    | Replace internal basic UI (or use `/apply-composites`)    |
| **Business**    | UserCard, OrderList  | Keep structure, replace internals                         |
| **Layout**      | Header, Sidebar      | Use AppShell or `/apply-composites` skill                 |

---

## Phase 2: Mapping Analysis

### 2-1. Custom Component → @l-fin/ui-components Mapping

Analyze each custom component to determine equivalent. Reference `src/llms/llms.txt` for available components.

```markdown
| Custom Component | Props                     | @l-fin Equivalent             | Difficulty |
| ---------------- | ------------------------- | ----------------------------- | ---------- |
| `<Btn>`          | label, onClick, disabled  | `<Button>`                    | Low        |
| `<CustomInput>`  | value, onChange, error    | `<TextInput>`                 | Low        |
| `<Popup>`        | isOpen, onClose, children | `<Modal>`                     | Medium     |
| `<DataGrid>`     | columns, data, pagination | `/apply-composites` skill     | Medium     |
| `<Sidebar>`      | items, collapsed          | `/apply-composites` skill     | Medium     |
| `<FilterForm>`   | filters, onSubmit         | `/apply-composites` skill     | Low        |
```

### 2-2. Equivalent Components by Purpose

| Purpose               | @l-fin/ui-components                               |
| --------------------- | -------------------------------------------------- |
| **Button actions**    | Button, ActionIcon, CloseButton, CopyButton        |
| **Text input**        | TextInput, Textarea, PasswordInput, NumberInput    |
| **Selection**         | Select, MultiSelect, Checkbox, Radio, Switch, Chip |
| **Data display**      | Table, Card, Badge, Accordion                      |
| **Overlays/Dialogs**  | Modal, Drawer, Popover, Menu, Tooltip              |
| **Navigation**        | Tabs, Pagination, Stepper                          |
| **Layout**            | Grid, Stack, Group, Flex                           |
| **Feedback**          | Loader, Skeleton, Notification                     |
| **Date/Time**         | DatePickerInput, Calendar, ScrollTimePicker        |

> **Note**: For complex tables (DataTable), filter forms (FilterBuilder), layouts (AppLayout), see `/apply-composites` skill.

### 2-3. Document Props Mapping

```tsx
// Custom component
interface BtnProps {
  label: string;
  onClick: () => void;
  disabled?: boolean;
  type?: 'primary' | 'secondary';
}

// @l-fin/ui-components mapping
// label → children
// type: 'primary' → variant: 'filled'
// type: 'secondary' → variant: 'outline'

// Additional available features:
// preventRapidClick: rapid click prevention
// autoLoading: auto loading for async onClick
```

---

## Phase 3: Incremental Conversion

### 3-1. Conversion Priority

1. **Basic UI components** (Button, Input, etc.) - High impact
2. **Layout components** - Determines overall structure
3. **Composite components** - Auto-reflected after basic UI conversion
4. **Business components** - Replace internals only as needed

### 3-2. Wrapper Strategy (Optional)

When existing component interface must be preserved:

```tsx
// Wrapper preserving existing interface
import {Button as LfinButton} from '@l-fin/ui-components';

interface BtnProps {
  label: string;
  onClick: () => void;
  type?: 'primary' | 'secondary';
}

export function Btn({label, onClick, type = 'primary'}: BtnProps) {
  return (
    <LfinButton variant={type === 'primary' ? 'filled' : 'outline'} onClick={onClick}>
      {label}
    </LfinButton>
  );
}
```

### 3-3. Direct Replacement

When replacing without wrapper, modify all usage locations:

```bash
# Find component usage
Grep pattern="<Btn" glob="*.{ts,tsx}" path="src/"
```

---

## Phase 4: Style Conversion

### 4-1. Analyze Existing Style System

| Style Method      | Conversion Approach                       |
| ----------------- | ----------------------------------------- |
| CSS Modules       | Keep className or convert to Tailwind     |
| Styled Components | Mantine `styles` prop or Tailwind         |
| Inline styles     | Keep `style` prop                         |
| SCSS/SASS         | Convert to Tailwind or CSS Modules        |

### 4-2. Design Token Mapping

```tsx
// Existing tokens
const colors = {
  primary: '#0066FF',
  secondary: '#6B7280',
};

// Option 1: Use @l-fin/ui-components default theme (recommended)
// LFin design tokens auto-applied

// Option 2: Customize with createAppTheme
import { createAppTheme } from "@l-fin/ui-components";

const myTheme = createAppTheme({
  tokens: {
    colors: {
      primary: ['#E3F2FD', ..., '#0D47A1'],  // 10-step palette
    },
  },
  mantine: {
    primaryColor: 'primary',
  },
});
```

---

## Search Commands

```bash
# Find custom component folders
Glob pattern="src/**/components/**/*.tsx"
Glob pattern="src/**/ui/**/*.tsx"

# Find specific component usage
Grep pattern="<CustomComponent" glob="*.{ts,tsx}" path="src/"

# Find Props type definitions
Grep pattern="interface.*Props" glob="*.{ts,tsx}" path="src/components/"
```

---

## Extra Features

LFin-specific features available after migration:

| Component       | Extra Props                                                     |
| --------------- | --------------------------------------------------------------- |
| `Button`        | `preventRapidClick`, `autoLoading`                              |
| `ActionIcon`    | `preventRapidClick`, `autoLoading`                              |
| `PasswordInput` | `showStrength`, `showCapsLockWarning`, `showGenerator`          |
| `Image`         | `withPreview`, `showDownloadButton`, `zoomable`, `fallbackText` |

> **Composite components** (DataTable, FilterBuilder, AppLayout): See `/apply-composites` skill.

---

## Notes

1. **Not fully automatable** - Requires project-specific analysis
2. **Incremental approach required** - Big bang conversion is risky
3. **Consider wrapper strategy** - When existing interface must be preserved
4. **Separate business logic** - Preserve logic during UI conversion
5. **Test coverage** - Recommended to establish tests before conversion
6. **Notifications in ThemeProvider** - Remove existing notification components
