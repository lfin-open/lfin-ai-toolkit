# Mantine → @l-fin/ui-components

Migration guide from @mantine/* packages to @l-fin/ui-components.

## Overview

| Item          | Value             |
| ------------- | ----------------- |
| Difficulty    | Low               |
| Automatable   | 90%+              |
| Main work     | Import path change|

@l-fin/ui-components wraps Mantine, so most component/hook APIs are identical.

---

## Migratable Items

### @mantine/core → @l-fin/ui-components

```tsx
// Before
import {Button, Group, Stack, Text, Title} from '@mantine/core';

// After
import {Button, Group, Stack, Text, Title} from '@l-fin/ui-components';
```

**Components by category**:

| Category        | Components                                                                                      |
| --------------- | ----------------------------------------------------------------------------------------------- |
| **Buttons**     | `Button`, `ActionIcon`, `CloseButton`, `CopyButton`, `FileButton`                               |
| **Combobox**    | `Autocomplete`, `Combobox`, `MultiSelect`, `Pill`, `PillsInput`, `Select`, `TagsInput`          |
| **DataDisplay** | `Accordion`, `Badge`, `Card`, `Indicator`, `Spoiler`                                            |
| **Feedback**    | `Loader`, `Notification`, `Skeleton`                                                            |
| **Inputs**      | `Checkbox`, `Chip`, `NativeSelect`, `PinInput`, `Radio`, `SegmentedControl`, `Slider`, `Switch` |
| **TextInputs**  | `FileInput`, `NumberInput`, `PasswordInput`, `Textarea`, `TextInput`                            |
| **Navigation**  | `Pagination`, `Stepper`, `Tabs`                                                                 |
| **Overlays**    | `Drawer`, `Menu`, `Modal`, `Overlay`, `Popover`, `Tooltip`                                      |
| **Layout**      | `Box`, `Flex`, `Group`, `Stack`, `Grid`, `GridCol`, `AppShell`, `ScrollArea`                    |
| **Typography**  | `Text`, `Title`, `Anchor`                                                                       |
| **Misc**        | `Divider`, `Table`, `Collapse`, `ThemeIcon`, `Paper`, `Alert`                                   |

### @mantine/dates → @l-fin/ui-components

@l-fin/ui-components provides a **unified `DatePicker`** component with `pickerType` prop:

```tsx
// Before
import { DatePickerInput, DateTimePicker, MonthPickerInput } from "@mantine/dates";

// After
import { DatePicker } from "@l-fin/ui-components";

<DatePicker pickerType="date" />      // DatePickerInput
<DatePicker pickerType="datetime" />  // DateTimePicker
<DatePicker pickerType="month" />     // MonthPickerInput
```

**Date component mapping**:

| @mantine/dates     | @l-fin/ui-components                                                  |
| ------------------ | --------------------------------------------------------------------- |
| `Calendar`         | `Calendar` or `<DatePicker pickerType="calendar" />`                  |
| `DatePickerInput`  | `DatePickerInput` or `<DatePicker pickerType="date" />`               |
| `DateTimePicker`   | `<DatePicker pickerType="datetime" />`                                |
| `MonthPickerInput` | `<DatePicker pickerType="month" />`                                   |
| `YearPickerInput`  | `<DatePicker pickerType="year" />`                                    |
| `TimePicker`       | `<DatePicker pickerType="time" />` (auto ScrollTimePicker on mobile)  |

### @mantine/hooks → @l-fin/ui-components

```tsx
// Before
import {useDisclosure, useClickOutside} from '@mantine/hooks';

// After
import {useDisclosure, useClickOutside} from '@l-fin/ui-components';
```

**Hooks by category**:

| Category      | Hooks                                                                                                                                                                                                                                                                                             |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **State**     | `useDisclosure`, `useCounter`, `useToggle`, `useLocalStorage`, `useSessionStorage`, `useDebouncedValue`, `useDebouncedState`, `useThrottledValue`, `useThrottledState`                                                                                                                            |
| **UI/DOM**    | `useClickOutside`, `useClipboard`, `useColorScheme`, `useElementSize`, `useFocusWithin`, `useFullscreen`, `useHotkeys`, `useHover`, `useIntersection`, `useMediaQuery`, `useMouse`, `useMove`, `useReducedMotion`, `useResizeObserver`, `useScrollIntoView`, `useViewportSize`, `useWindowScroll` |
| **Utilities** | `useDocumentTitle`, `useDocumentVisibility`, `useFetch`, `useIdle`, `useInterval`, `useMergedRef`, `useNetwork`, `useOs`, `useTimeout`                                                                                                                                                            |

### Theme Hooks

```tsx
// Before
import {useMantineTheme, useMantineColorScheme} from '@mantine/core';

// After
import {useMantineTheme, useMantineColorScheme} from '@l-fin/ui-components';
```

---

## Non-migratable Items (Keep Original)

### @mantine/core (Some)

```tsx
// Keep importing from @mantine/core
import {useCombobox, InputBase, Fieldset, ColorInput, JsonInput, Rating} from '@mantine/core';
```

| Item          | Reason                        |
| ------------- | ----------------------------- |
| `useCombobox` | Low-level hook for custom combobox |
| `InputBase`   | Low-level input component     |
| `Fieldset`    | Low usage frequency           |
| `ColorInput`  | Low usage frequency           |
| `JsonInput`   | Low usage frequency           |
| `Rating`      | Low usage frequency           |

### @mantine/dates (Some)

```tsx
// Low-level components - import directly from @mantine/dates
import {
  DateInput,
  TimeInput,
  TimeGrid,
  MiniCalendar,
  MonthPicker,
  YearPicker,
} from '@mantine/dates';
```

### @mantine/notifications

```tsx
// Keep notifications API
import {notifications} from '@mantine/notifications';

notifications.show({
  title: '성공',
  message: '저장되었습니다',
  color: 'success',
});
```

**Note**: ThemeProvider includes `<Notifications />` internally (enabled by default). Remove existing `<Notifications />` component to avoid duplicates.

---

## Migration Steps

### Phase 1: Provider Replacement

```tsx
// Before
import {MantineProvider} from '@mantine/core';
import {Notifications} from '@mantine/notifications';

<MantineProvider>
  <Notifications />
  <App />
</MantineProvider>;

// After
import {ThemeProvider} from '@l-fin/ui-components';

<ThemeProvider>
  <App />
</ThemeProvider>;
```

**Note**: ThemeProvider has built-in Notifications. Remove `<Notifications />` component.

### Phase 2: Bulk Import Path Change

**Conversion rules**:

| Source                   | Target                 | Note                                                      |
| ------------------------ | ---------------------- | --------------------------------------------------------- |
| `@mantine/core`          | `@l-fin/ui-components` | Most components                                           |
| `@mantine/hooks`         | `@l-fin/ui-components` | All hooks                                                 |
| `@mantine/dates`         | `@l-fin/ui-components` | Most components (see pickerType mapping)                  |
| `@mantine/dates`         | Keep                   | Low-level only: DateInput, TimeInput, TimeGrid, MiniCalendar |
| `@mantine/notifications` | Keep                   | `notifications.show()` API                                |

**Per-file handling**:

```tsx
// Before
import {Button, Group, useMantineTheme} from '@mantine/core';
import {useDisclosure} from '@mantine/hooks';
import {DatePickerInput} from '@mantine/dates';

// After
import {Button, Group, useMantineTheme, useDisclosure, DatePickerInput} from '@l-fin/ui-components';
```

### Phase 3: Exception Handling

Mixed imports needed for non-re-exported items:

```tsx
// Mixed imports allowed
import {Button, Group, Select} from '@l-fin/ui-components';
import {useCombobox, InputBase} from '@mantine/core';
import {notifications} from '@mantine/notifications';
```

### Phase 4: Custom Theme Removal

If using custom `mantine-theme.ts`:

```tsx
// Before
import { theme } from "./mantine-theme";
<MantineProvider theme={theme}>

// After - use @l-fin/ui-components default theme
<ThemeProvider>
// Or customize with createAppTheme
import { ThemeProvider, createAppTheme } from "@l-fin/ui-components";
const myTheme = createAppTheme({ mantine: { primaryColor: 'teal' } });
<ThemeProvider theme={myTheme}>
```

---

## Search Commands

Find target files:

```bash
# Find @mantine/core imports
Grep pattern="from ['\"]@mantine/core['\"]" glob="*.{ts,tsx}" path="src/"

# Find @mantine/hooks imports
Grep pattern="from ['\"]@mantine/hooks['\"]" glob="*.{ts,tsx}" path="src/"

# Find @mantine/dates imports
Grep pattern="from ['\"]@mantine/dates['\"]" glob="*.{ts,tsx}" path="src/"

# Find MantineProvider usage
Grep pattern="MantineProvider" glob="*.{ts,tsx}" path="src/"

# Find Notifications component
Grep pattern="<Notifications" glob="*.{ts,tsx}" path="src/"
```

---

## Extra Features

LFin-specific features available after migration:

| Component                      | Extra Props                                                     |
| ------------------------------ | --------------------------------------------------------------- |
| `Button`                       | `preventRapidClick`, `autoLoading`                              |
| `ActionIcon`                   | `preventRapidClick`, `autoLoading`                              |
| `PasswordInput`                | `showStrength`, `showCapsLockWarning`, `showGenerator`          |
| `Image`                        | `withPreview`, `showDownloadButton`, `zoomable`, `fallbackText` |
| `Notification`                 | `variant` (default/success/error/loading with preset icons)     |
| `DatePicker pickerType="time"` | **Responsive**: Auto ScrollTimePicker on mobile (≤767px)        |

> **Composite components** (DataTable, FilterBuilder, AppLayout): See `/apply-composites` skill.

---

## Notes

1. **Type imports**: `import type { ModalProps }` also needs path change
2. **Notifications**: Remove `<Notifications />` component (included in ThemeProvider)
3. **Theme customization**: Delete mantine-theme.ts, use @l-fin/ui-components default or `createAppTheme`
4. **DatesProvider**: ThemeProvider includes Korean locale DatesProvider - remove existing one
5. **Unified DatePicker**: Use `pickerType` prop instead of individual components (DateTimePicker, MonthPickerInput, etc.)
