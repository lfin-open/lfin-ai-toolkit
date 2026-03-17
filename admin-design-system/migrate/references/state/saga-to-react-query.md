# Redux-Saga → React Query

Migration guide from Redux-Saga to React Query (TanStack Query).

## Overview

| Item          | Value                                |
| ------------- | ------------------------------------ |
| Difficulty    | Medium                               |
| Automatable   | 50~60%                               |
| Main work     | saga → useQuery/useMutation + service|

---

## Concept Mapping

| Redux-Saga           | React Query              |
| -------------------- | ------------------------ |
| `takeLatest`         | `useQuery` (auto dedupe) |
| `call(apiService)`   | `queryFn`                |
| `put(successAction)` | Auto cache update        |
| `put(loadingAction)` | `isLoading` state        |
| `select(selector)`   | Zustand store reference  |
| `yield` chain        | `async/await`            |

---

## Basic Conversion Pattern

### Before: Saga with services

```js
// saga.js
import { call, put, select, takeLatest } from 'redux-saga/effects';
import { loadLogListSuccess } from './actions';
import { LOAD_LOG_LIST } from './constants';
import { changeLoadingIndicatorStatus } from 'containers/App/actions';
import { makeSelectFromDate, makeSelectToDate, makeSelectLogListPage } from './selector';
import { getLogListService } from './services';

export function* getLogList() {
  const fromDate = yield select(makeSelectFromDate());
  const toDate = yield select(makeSelectToDate());
  const page = yield select(makeSelectLogListPage());

  try {
    yield put(changeLoadingIndicatorStatus(true));

    const response = yield call(getLogListService, fromDate, toDate, page);

    if (!response) return;

    yield put(loadLogListSuccess(response.data));
  } catch (ignore) {
    // ignore
  } finally {
    yield put(changeLoadingIndicatorStatus(false));
  }
}

export default function* logSaga() {
  yield takeLatest(LOAD_LOG_LIST, getLogList);
}
```

```js
// services.js
import request from 'utils/request';
import buildUrl from 'build-url';
import moment from 'moment';

export async function getLogListService(fromDate, toDate, page, size) {
  const requestUrl = buildUrl(BASE_URL, {
    path: '/logs',
    queryParams: {
      startDate: moment(fromDate).format('YYYY-MM-DD'),
      endDate: moment(toDate).format('YYYY-MM-DD'),
      page,
      size,
    },
  });

  const response = await request(requestUrl, DEFAULT_OPTIONS);
  return response;
}
```

### After: React Query with service

```ts
// services/logs.ts
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { useLogStore } from '@/stores/useLogStore';
import dayjs from 'dayjs';

// API function (pure function)
async function fetchLogList(params: LogListParams): Promise<LogListResponse> {
  const searchParams = new URLSearchParams({
    startDate: dayjs(params.fromDate).format('YYYY-MM-DD'),
    endDate: dayjs(params.toDate).format('YYYY-MM-DD'),
    page: String(params.page),
    size: String(params.size),
    ...(params.requestId && { requestId: params.requestId }),
    ...(params.serviceName && { serviceName: params.serviceName }),
  });

  const response = await fetch(`${BASE_URL}/logs?${searchParams}`, {
    headers: getAuthHeaders(),
  });

  if (!response.ok) {
    throw new Error('로그 조회 중 오류가 발생하였습니다.');
  }

  return response.json();
}

// Query Hook
export function useLogList() {
  // Get filter conditions from Zustand
  const filters = useLogStore((state) => ({
    requestId: state.requestId,
    serviceName: state.serviceName,
    geoType: state.geoType,
    result: state.result,
    fromDate: state.fromDate,
    toDate: state.toDate,
    page: state.page,
    size: state.size,
  }));

  return useQuery({
    queryKey: ['logs', filters],
    queryFn: () => fetchLogList(filters),
    staleTime: 1000 * 60, // 1 minute
  });
}

// Detail query
async function fetchLogDetail(requestId: string): Promise<LogDetail> {
  const response = await fetch(`${BASE_URL}/logs/${requestId}`, {
    headers: getAuthHeaders(),
  });

  if (!response.ok) {
    throw new Error('로그 상세정보를 불러오는 중 오류가 발생하였습니다.');
  }

  return response.json();
}

export function useLogDetail(requestId: string | null) {
  return useQuery({
    queryKey: ['log-detail', requestId],
    queryFn: () => fetchLogDetail(requestId!),
    enabled: !!requestId, // Only run when requestId exists
  });
}
```

---

## Component Usage

### Before: Container with saga dispatch

```jsx
// containers/Logs/index.js
function LogList({ events, page, onLoadLogList, onChangeLogListPage }) {
  useInjectSaga({ key, saga });

  useEffect(() => {
    onLoadLogList();
  }, [fromDate, toDate]);

  return (
    <LogListTable
      data={events.content}
      page={page}
      onChangePage={(newPage) => {
        onChangeLogListPage(newPage);
        onLoadLogList();
      }}
    />
  );
}
```

### After: Page with React Query

```tsx
// routes/main/logs.tsx
import { useLogList } from '@/services/logs';
import { useLogStore } from '@/stores/useLogStore';

function LogsPage() {
  const { page, setPage } = useLogStore();
  const { data, isLoading, error } = useLogList();

  // Auto refetch on page change (page in queryKey)
  const handlePageChange = (newPage: number) => {
    setPage(newPage);
    // React Query auto-refetches on queryKey change
  };

  if (isLoading) return <LoadingOverlay visible />;
  if (error) return <Text c="red">{error.message}</Text>;

  return (
    <DataTable
      records={data?.content ?? []}
      page={page}
      onPageChange={handlePageChange}
      totalRecords={data?.totalElements ?? 0}
    />
  );
}
```

---

## Loading State Handling

### Before: Global loading indicator

```js
// saga.js
export function* getLogList() {
  try {
    yield put(changeLoadingIndicatorStatus(true));
    // ...
  } finally {
    yield put(changeLoadingIndicatorStatus(false));
  }
}

// App/index.js
function App({ loading }) {
  return (
    <>
      {loading && <LoadingIndicator />}
      {/* ... */}
    </>
  );
}
```

### After: Per-component loading state

```tsx
// routes/main/logs.tsx
function LogsPage() {
  const { data, isLoading, isFetching } = useLogList();

  return (
    <Box pos="relative">
      <LoadingOverlay visible={isLoading} />
      {/* isFetching: background refetch in progress (optional display) */}
      <DataTable records={data?.content ?? []} />
    </Box>
  );
}

// Or if global loading is needed
function App() {
  const isFetching = useIsFetching(); // Total fetching query count

  return (
    <>
      {isFetching > 0 && <GlobalLoadingBar />}
      {/* ... */}
    </>
  );
}
```

---

## Mutation (Create/Update/Delete)

### Before: Saga with dispatch

```js
// saga.js
export function* deleteCache({ requestIds }) {
  try {
    yield put(changeLoadingIndicatorStatus(true));
    yield call(deleteCacheService, requestIds);
    yield put(deleteCacheSuccess());
    yield put(loadCacheList()); // Refresh list
    alert('삭제되었습니다.');
  } catch (e) {
    alert('삭제 중 오류가 발생하였습니다.');
  } finally {
    yield put(changeLoadingIndicatorStatus(false));
  }
}
```

### After: useMutation

```ts
// services/caches.ts
import { useMutation, useQueryClient } from '@tanstack/react-query';
import { notifications } from '@mantine/notifications';

async function deleteCache(requestIds: string[]): Promise<void> {
  const response = await fetch(`${BASE_URL}/caches`, {
    method: 'DELETE',
    headers: getAuthHeaders(),
    body: JSON.stringify({ requestIds }),
  });

  if (!response.ok) {
    throw new Error('삭제 중 오류가 발생하였습니다.');
  }
}

export function useDeleteCache() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: deleteCache,
    onSuccess: () => {
      // Refresh cache list
      queryClient.invalidateQueries({ queryKey: ['caches'] });
      notifications.show({
        title: '성공',
        message: '삭제되었습니다.',
        color: 'green',
      });
    },
    onError: (error) => {
      notifications.show({
        title: '오류',
        message: error.message,
        color: 'red',
      });
    },
  });
}
```

```tsx
// Component usage
function DeleteCacheModal({ requestIds, onClose }) {
  const { mutate: deleteCache, isPending } = useDeleteCache();

  const handleDelete = () => {
    deleteCache(requestIds, {
      onSuccess: () => onClose(),
    });
  };

  return (
    <Modal opened onClose={onClose}>
      <Text>선택한 {requestIds.length}개 항목을 삭제하시겠습니까?</Text>
      <Group justify="flex-end" mt="md">
        <Button variant="outline" onClick={onClose}>취소</Button>
        <Button color="red" onClick={handleDelete} loading={isPending}>
          삭제
        </Button>
      </Group>
    </Modal>
  );
}
```

---

## Validation Logic Migration

### Before: Validation in service

```js
// services.js
export async function getLogListService(fromDate, toDate, page, size) {
  if (!isValidDateRange(fromDate, toDate)) {
    alert('조회 기간이 올바르지 않습니다.');
    return null;
  }
  if (getDateRangeDiff(fromDate, toDate) > 90) {
    alert('최대 90일까지 조회 가능합니다.');
    return null;
  }
  // ... API call
}
```

### After: Validation in query or component

```ts
// services/logs.ts
export function useLogList() {
  const filters = useLogStore(selectFilters);

  return useQuery({
    queryKey: ['logs', filters],
    queryFn: () => fetchLogList(filters),
    enabled: isValidDateRange(filters.fromDate, filters.toDate), // Conditional execution
  });
}

// Or validate in component
function LogSearchCard() {
  const { fromDate, toDate, setFilters } = useLogStore();

  const handleSearch = () => {
    if (!isValidDateRange(fromDate, toDate)) {
      notifications.show({ message: '조회 기간이 올바르지 않습니다.', color: 'red' });
      return;
    }
    if (getDateRangeDiff(fromDate, toDate) > 90) {
      notifications.show({ message: '최대 90일까지 조회 가능합니다.', color: 'red' });
      return;
    }
    // Auto refetch on queryKey change
  };
}
```

---

## Provider Setup

```tsx
// providers/QueryProvider.tsx
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { ReactQueryDevtools } from '@tanstack/react-query-devtools';

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 1000 * 60, // 1 minute
      retry: 1,
      refetchOnWindowFocus: false,
    },
  },
});

export function QueryProvider({ children }: { children: React.ReactNode }) {
  return (
    <QueryClientProvider client={queryClient}>
      {children}
      <ReactQueryDevtools initialIsOpen={false} />
    </QueryClientProvider>
  );
}
```

---

## Search Commands

```bash
# Find saga files
Glob pattern="prev/**/saga.js"

# Find takeLatest usage
Grep pattern="takeLatest" glob="*.js" path="prev/"

# Find services files
Glob pattern="prev/**/services.js"

# Find yield call pattern
Grep pattern="yield call" glob="*.js" path="prev/"
```

---

## Removal Targets

Remove after migration:

1. **Packages**:
   - `redux-saga`

2. **Files**:
   - `containers/*/saga.js`
   - `utils/injectSaga.js`

```bash
pnpm remove redux-saga
```

---

## Notes

1. **Caching**: React Query provides automatic caching and deduplication
2. **Refetch**: Auto-refetch on queryKey change, manual via `invalidateQueries`
3. **Error Handling**: Use `onError` callback or Error Boundary
4. **Loading**: Distinguish `isLoading` (first load) vs `isFetching` (all loads)
5. **Optimistic Updates**: Use `onMutate` for optimistic updates
6. **alert → notifications**: Use `@mantine/notifications` instead of `alert()`
