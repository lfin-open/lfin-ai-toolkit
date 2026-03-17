# moment.js → Day.js

Migration guide from moment.js to Day.js.

## Overview

| Item       | Value                              |
| ---------- | ---------------------------------- |
| Difficulty | Low                                |
| Automatable| 80~90%                             |
| Main work  | Import changes + some API adjustments |

---

## Basic Conversion

### Import Change

```js
// Before
import moment from 'moment';

// After
import dayjs from 'dayjs';
```

### Most APIs are Compatible

Day.js provides almost identical API to moment.js:

```js
// Current time - same
moment()          → dayjs()

// Parsing - same
moment('2024-01-15')  → dayjs('2024-01-15')

// Format - same
moment().format('YYYY-MM-DD')  → dayjs().format('YYYY-MM-DD')

// Manipulation - same
moment().add(1, 'day')     → dayjs().add(1, 'day')
moment().subtract(1, 'month')  → dayjs().subtract(1, 'month')

// Start/End - same
moment().startOf('day')   → dayjs().startOf('day')
moment().endOf('month')   → dayjs().endOf('month')

// Comparison - same
moment().isBefore(other)  → dayjs().isBefore(other)
moment().isAfter(other)   → dayjs().isAfter(other)
moment().isSame(other)    → dayjs().isSame(other)

// Difference - same
moment().diff(other, 'days')  → dayjs().diff(other, 'days')
```

---

## Project-specific Conversion

### initialState with moment

```js
// Before: reducer.js
import moment from 'moment';

export const initialState = {
  fromDate: moment().startOf('day'),
  toDate: moment(),
};
```

```ts
// After: stores/useLogStore.ts
import dayjs, { Dayjs } from 'dayjs';

interface LogState {
  fromDate: Dayjs;
  toDate: Dayjs;
}

const initialState = {
  fromDate: dayjs().startOf('day'),
  toDate: dayjs(),
};
```

### Date Formatting

```js
// Before: services.js
import moment from 'moment';

const startDate = moment(fromDate).format('YYYY-MM-DD').toString();
const endDate = moment(toDate).format('YYYY-MM-DD').toString();
```

```ts
// After: services/logs.ts
import dayjs from 'dayjs';

const startDate = dayjs(fromDate).format('YYYY-MM-DD');
const endDate = dayjs(toDate).format('YYYY-MM-DD');
```

### Table Cell Formatting

```jsx
// Before
import moment from 'moment';

<StyledTableCell>
  {moment(row.createdAt).format('YYYY-MM-DD HH:mm:ss')}
</StyledTableCell>
```

```tsx
// After
import dayjs from 'dayjs';

<Table.Td>
  {dayjs(row.createdAt).format('YYYY-MM-DD HH:mm:ss')}
</Table.Td>
```

---

## Plugin Required Features

Some features require plugins:

### isSameOrBefore / isSameOrAfter

```ts
// dayjs setup (main.tsx or separate file)
import dayjs from 'dayjs';
import isSameOrBefore from 'dayjs/plugin/isSameOrBefore';
import isSameOrAfter from 'dayjs/plugin/isSameOrAfter';

dayjs.extend(isSameOrBefore);
dayjs.extend(isSameOrAfter);

// Usage
dayjs().isSameOrBefore(other);
dayjs().isSameOrAfter(other);
```

### Relative Time (fromNow)

```ts
import dayjs from 'dayjs';
import relativeTime from 'dayjs/plugin/relativeTime';
import 'dayjs/locale/ko';

dayjs.extend(relativeTime);
dayjs.locale('ko');

// Usage
dayjs().fromNow();  // "몇 초 전"
```

### Duration

```ts
import dayjs from 'dayjs';
import duration from 'dayjs/plugin/duration';

dayjs.extend(duration);

// Usage
dayjs.duration(1, 'hour').asMinutes();  // 60
```

---

## Utility Function Conversion

### Before: datetimeHelper.js

```js
// commons/datetimeHelper.js
import moment from 'moment';

export function isValidDateRange(fromDate, toDate) {
  return moment(fromDate).isSameOrBefore(moment(toDate));
}

export function getDateRangeDiff(fromDate, toDate) {
  return moment(toDate).diff(moment(fromDate), 'days');
}
```

### After: lib/datetime.ts

```ts
// lib/datetime.ts
import dayjs, { Dayjs } from 'dayjs';
import isSameOrBefore from 'dayjs/plugin/isSameOrBefore';

dayjs.extend(isSameOrBefore);

export function isValidDateRange(fromDate: Dayjs | Date | string, toDate: Dayjs | Date | string): boolean {
  return dayjs(fromDate).isSameOrBefore(dayjs(toDate));
}

export function getDateRangeDiff(fromDate: Dayjs | Date | string, toDate: Dayjs | Date | string): number {
  return dayjs(toDate).diff(dayjs(fromDate), 'day');
}
```

---

## @l-fin/ui-components Integration

DatePicker component uses Day.js by default:

```tsx
import { DatePicker } from '@l-fin/ui-components';
import dayjs from 'dayjs';

function DateRangePicker() {
  const [fromDate, setFromDate] = useState<Date | null>(dayjs().startOf('day').toDate());
  const [toDate, setToDate] = useState<Date | null>(dayjs().toDate());

  return (
    <>
      <DatePicker
        pickerType="date"
        label="시작일"
        value={fromDate}
        onChange={setFromDate}
      />
      <DatePicker
        pickerType="date"
        label="종료일"
        value={toDate}
        onChange={setToDate}
      />
    </>
  );
}
```

---

## dayjs Initial Setup

```ts
// lib/dayjs.ts
import dayjs from 'dayjs';
import 'dayjs/locale/ko';
import isSameOrBefore from 'dayjs/plugin/isSameOrBefore';
import isSameOrAfter from 'dayjs/plugin/isSameOrAfter';
import customParseFormat from 'dayjs/plugin/customParseFormat';

// Register plugins
dayjs.extend(isSameOrBefore);
dayjs.extend(isSameOrAfter);
dayjs.extend(customParseFormat);

// Korean locale
dayjs.locale('ko');

export default dayjs;
```

```ts
// main.tsx
import './lib/dayjs'; // Initialize
```

---

## Search Commands

```bash
# Find moment imports
Grep pattern="from ['\"]moment['\"]" glob="*.{js,jsx}" path="prev/"

# Find moment() usage
Grep pattern="moment\\(" glob="*.{js,jsx}" path="prev/"

# Find moment format usage
Grep pattern="\\.format\\(" glob="*.{js,jsx}" path="prev/"
```

---

## Removal Targets

Remove after migration:

1. **Packages**:
   - `moment`
   - `@date-io/moment`

```bash
pnpm remove moment @date-io/moment
```

---

## Notes

1. **Bundle size**: Day.js (~2KB) vs moment.js (~300KB)
2. **Immutability**: Day.js is immutable, moment is mutable
3. **Plugins**: Some features require plugins
4. **Types**: Use `Dayjs` type (moment uses `Moment`)
5. **Locales**: Import only needed locales
