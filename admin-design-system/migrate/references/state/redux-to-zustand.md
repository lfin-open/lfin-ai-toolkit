# Redux → Zustand

Migration guide from Redux + immer to Zustand.

## Overview

| Item          | Value                                |
| ------------- | ------------------------------------ |
| Difficulty    | Medium                               |
| Automatable   | 60~70%                               |
| Main work     | reducer + actions + selector → store |

---

## File Structure Conversion

### Before (Redux)

```
containers/Logs/
├── actions.js       # Action creators
├── constants.js     # Action types
├── reducer.js       # Reducer with immer
├── selector.js      # Reselect selectors
├── saga.js          # Side effects (separate migration)
├── services.js      # API calls
└── index.js         # Container component
```

### After (Zustand)

```
stores/
└── useLogStore.ts   # All state + actions combined

services/
└── logs.ts          # API (use with React Query)
```

---

## Basic Conversion Pattern

### Before: Redux reducer + actions + constants

```js
// constants.js
export const CHANGE_LOG_LIST_PAGE = 'app/Logs/CHANGE_LOG_LIST_PAGE';
export const LOAD_LOG_LIST_SUCCESS = 'app/Logs/LOAD_LOG_LIST_SUCCESS';

// actions.js
export function changeLogListPage(page) {
  return { type: CHANGE_LOG_LIST_PAGE, page };
}
export function loadLogListSuccess(events) {
  return { type: LOAD_LOG_LIST_SUCCESS, events };
}

// reducer.js
import produce from 'immer';
import { CHANGE_LOG_LIST_PAGE, LOAD_LOG_LIST_SUCCESS } from './constants';

export const initialState = {
  page: 0,
  size: 10,
  events: [],
};

const logListReducer = (state = initialState, action) =>
  produce(state, draft => {
    switch (action.type) {
      case CHANGE_LOG_LIST_PAGE:
        draft.page = action.page;
        break;
      case LOAD_LOG_LIST_SUCCESS:
        draft.events = action.events;
        break;
    }
  });

// selector.js
import { createSelector } from 'reselect';

const selectLogListDomain = state => state.logList;

export const makeSelectLogListPage = () =>
  createSelector(selectLogListDomain, substate => substate.page);
```

### After: Zustand store

```ts
// stores/useLogStore.ts
import { create } from 'zustand';
import { devtools } from 'zustand/middleware';
import dayjs from 'dayjs';

interface LogState {
  // State
  page: number;
  size: number;
  events: LogEvent[];

  // Actions
  setPage: (page: number) => void;
  setSize: (size: number) => void;
  setEvents: (events: LogEvent[]) => void;
  reset: () => void;
}

const initialState = {
  page: 0,
  size: 10,
  events: [],
};

export const useLogStore = create<LogState>()(
  devtools(
    (set) => ({
      ...initialState,

      setPage: (page) => set({ page }, false, 'setPage'),
      setSize: (size) => set({ size }, false, 'setSize'),
      setEvents: (events) => set({ events }, false, 'setEvents'),
      reset: () => set(initialState, false, 'reset'),
    }),
    { name: 'log-store' }
  )
);
```

---

## Complex State Conversion

### Before: Form search conditions

```js
// reducer.js
export const initialState = {
  requestId: '',
  serviceName: '',
  geoType: ' ',
  result: ' ',
  code: '',
  address: '',
  fromDate: moment().startOf('day'),
  toDate: moment(),
  page: 0,
  size: 10,
  events: [],
  logDetail: [],
  isVisibleLogDetailModal: false,
};

const logListReducer = (state = initialState, action) =>
  produce(state, draft => {
    switch (action.type) {
      case CHANGE_LOG_LIST_REQUEST_ID:
        draft.requestId = action.requestId;
        break;
      case CHANGE_LOG_LIST_FROM_DATE:
        draft.fromDate = action.fromDate;
        break;
      case RESET_FORM:
        draft.requestId = '';
        draft.fromDate = moment().startOf('day');
        // ... reset all fields
        break;
    }
  });
```

### After: Zustand with slices

```ts
// stores/useLogStore.ts
import { create } from 'zustand';
import { devtools } from 'zustand/middleware';
import dayjs, { Dayjs } from 'dayjs';

// Filter types
interface LogFilters {
  requestId: string;
  serviceName: string;
  geoType: string;
  result: string;
  code: string;
  address: string;
  fromDate: Dayjs;
  toDate: Dayjs;
}

// Pagination types
interface LogPagination {
  page: number;
  size: number;
}

// Full state type
interface LogState extends LogFilters, LogPagination {
  events: LogEvent[];
  logDetail: LogDetail | null;
  isDetailModalOpen: boolean;

  // Filter actions
  setFilter: <K extends keyof LogFilters>(key: K, value: LogFilters[K]) => void;
  setFilters: (filters: Partial<LogFilters>) => void;
  resetFilters: () => void;

  // Pagination actions
  setPage: (page: number) => void;
  setSize: (size: number) => void;

  // Data actions
  setEvents: (events: LogEvent[]) => void;
  setLogDetail: (detail: LogDetail | null) => void;

  // Modal actions
  openDetailModal: () => void;
  closeDetailModal: () => void;
}

const initialFilters: LogFilters = {
  requestId: '',
  serviceName: '',
  geoType: '',
  result: '',
  code: '',
  address: '',
  fromDate: dayjs().startOf('day'),
  toDate: dayjs(),
};

export const useLogStore = create<LogState>()(
  devtools(
    (set) => ({
      ...initialFilters,
      page: 0,
      size: 10,
      events: [],
      logDetail: null,
      isDetailModalOpen: false,

      // Filter actions
      setFilter: (key, value) => set({ [key]: value }, false, `setFilter:${key}`),
      setFilters: (filters) => set(filters, false, 'setFilters'),
      resetFilters: () => set(initialFilters, false, 'resetFilters'),

      // Pagination actions
      setPage: (page) => set({ page }, false, 'setPage'),
      setSize: (size) => set({ size, page: 0 }, false, 'setSize'),

      // Data actions
      setEvents: (events) => set({ events }, false, 'setEvents'),
      setLogDetail: (logDetail) => set({ logDetail }, false, 'setLogDetail'),

      // Modal actions
      openDetailModal: () => set({ isDetailModalOpen: true }, false, 'openDetailModal'),
      closeDetailModal: () => set({ isDetailModalOpen: false, logDetail: null }, false, 'closeDetailModal'),
    }),
    { name: 'log-store' }
  )
);

// Optional: selector for computed values
export const useLogFilters = () => useLogStore((state) => ({
  requestId: state.requestId,
  serviceName: state.serviceName,
  geoType: state.geoType,
  result: state.result,
  code: state.code,
  address: state.address,
  fromDate: state.fromDate,
  toDate: state.toDate,
}));
```

---

## Component Connection Conversion

### Before: connect + mapStateToProps + mapDispatchToProps

```jsx
// containers/Logs/index.js
import { connect } from 'react-redux';
import { compose } from 'redux';
import { createStructuredSelector } from 'reselect';
import { makeSelectLogListPage, makeSelectEvents } from './selector';
import { changeLogListPage, loadLogList } from './actions';

function LogList({ page, events, onChangeLogListPage, onLoadLogList }) {
  // ...
}

const mapStateToProps = createStructuredSelector({
  page: makeSelectLogListPage(),
  events: makeSelectEvents(),
});

export function mapDispatchToProps(dispatch) {
  return {
    onChangeLogListPage: (page) => dispatch(changeLogListPage(page)),
    onLoadLogList: () => dispatch(loadLogList()),
  };
}

const withConnect = connect(mapStateToProps, mapDispatchToProps);

export default compose(withConnect, memo)(LogList);
```

### After: Zustand hooks

```tsx
// routes/main/logs.tsx
import { useLogStore } from '@/stores/useLogStore';
import { useLogList } from '@/services/logs';

function LogsPage() {
  // Select state (only what's needed)
  const page = useLogStore((state) => state.page);
  const setPage = useLogStore((state) => state.setPage);

  // Or select multiple states at once (use shallow comparison)
  const { page, size, setPage, setSize } = useLogStore(
    useShallow((state) => ({
      page: state.page,
      size: state.size,
      setPage: state.setPage,
      setSize: state.setSize,
    }))
  );

  // React Query for data fetching
  const { data: events, isLoading } = useLogList();

  return (
    // ...
  );
}
```

---

## Remove injectReducer / injectSaga

### Before: Dynamic reducer injection

```jsx
// containers/Logs/index.js
import { useInjectReducer } from 'utils/injectReducer';
import { useInjectSaga } from 'utils/injectSaga';
import reducer from './reducer';
import saga from './saga';

const key = 'logList';

function LogList(props) {
  useInjectReducer({ key, reducer });
  useInjectSaga({ key, saga });
  // ...
}
```

### After: Not needed (Zustand doesn't require injection)

```tsx
// routes/main/logs.tsx
import { useLogStore } from '@/stores/useLogStore';

function LogsPage() {
  // Direct usage - no injection needed
  const { page, setPage } = useLogStore();
  // ...
}
```

---

## Search Commands

```bash
# Find Redux connect usage
Grep pattern="from ['\"]react-redux['\"]" glob="*.{js,jsx}" path="prev/"

# Find mapStateToProps
Grep pattern="mapStateToProps" glob="*.{js,jsx}" path="prev/"

# Find createStructuredSelector
Grep pattern="createStructuredSelector" glob="*.{js,jsx}" path="prev/"

# Find reducer files
Glob pattern="prev/**/reducer.js"

# Find actions files
Glob pattern="prev/**/actions.js"
```

---

## Removal Targets

Remove after migration:

1. **Packages**:
   - `redux`
   - `react-redux`
   - `reselect`
   - `immer` (built into Zustand)
   - `connected-react-router`

2. **Files**:
   - `containers/*/reducer.js`
   - `containers/*/actions.js`
   - `containers/*/constants.js`
   - `containers/*/selector.js`
   - `configureStore.js`
   - `reducers.js`
   - `utils/injectReducer.js`
   - `utils/injectSaga.js` (after saga migration)

```bash
pnpm remove redux react-redux reselect immer connected-react-router
```

---

## Notes

1. **Selector optimization**: Select only needed state in Zustand to minimize re-renders
2. **useShallow**: Use shallow comparison when selecting multiple states
3. **DevTools**: Connect to Redux DevTools via `devtools` middleware
4. **Persist**: Use `persist` middleware for localStorage if needed
5. **moment → dayjs**: Recommended to migrate date library together
