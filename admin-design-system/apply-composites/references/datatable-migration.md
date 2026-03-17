# DataTable Migration Guide

Migrate table implementations to the `DataTable` component.

## Migration Target Identification

### Pattern A: Direct TanStack Table Usage

```tsx
// Direct TanStack Table usage
import { useReactTable, getCoreRowModel, ... } from "@tanstack/react-table";

const table = useReactTable({
  data,
  columns,
  getCoreRowModel: getCoreRowModel(),
  getSortedRowModel: getSortedRowModel(),
  getPaginationRowModel: getPaginationRowModel(),
});

return (
  <Table>
    <Table.Thead>
      {table.getHeaderGroups().map(...)}
    </Table.Thead>
    <Table.Tbody>
      {table.getRowModel().rows.map(...)}
    </Table.Tbody>
  </Table>
);
```

### Pattern B: Basic Mantine Table Usage

```tsx
// Basic Mantine Table with manual rendering
import { Table, Pagination } from "@l-fin/ui-components";

const [currentPage, setCurrentPage] = useState(1);
const [perPage, setPerPage] = useState(10);

const paginated = data.slice((currentPage - 1) * perPage, currentPage * perPage);

return (
  <>
    <Table>
      <Table.Thead>
        <Table.Tr>
          <Table.Th>이름</Table.Th>
          <Table.Th>상태</Table.Th>
        </Table.Tr>
      </Table.Thead>
      <Table.Tbody>
        {paginated.map((item) => (
          <Table.Tr key={item.id}>
            <Table.Td>{item.name}</Table.Td>
            <Table.Td>{item.status}</Table.Td>
          </Table.Tr>
        ))}
      </Table.Tbody>
    </Table>
    <Pagination total={totalPages} value={currentPage} onChange={setCurrentPage} />
  </>
);
```

---

## Basic Mantine Table → DataTable Migration

### Before (Basic Mantine Table)

```tsx
import { useState, useMemo } from "react";
import { Table, Badge, Button, Pagination, Group } from "@l-fin/ui-components";

interface User {
  id: string;
  name: string;
  status: "active" | "inactive";
}

function UsersTable({ users }: { users: User[] }) {
  const [currentPage, setCurrentPage] = useState(1);
  const [perPage, setPerPage] = useState(10);

  const totalPages = Math.ceil(users.length / perPage);
  const paginated = users.slice((currentPage - 1) * perPage, currentPage * perPage);

  if (users.length === 0) {
    return <div>데이터가 없습니다</div>;
  }

  return (
    <>
      <Table>
        <Table.Thead>
          <Table.Tr>
            <Table.Th>이름</Table.Th>
            <Table.Th>상태</Table.Th>
            <Table.Th>액션</Table.Th>
          </Table.Tr>
        </Table.Thead>
        <Table.Tbody>
          {paginated.map((user) => (
            <Table.Tr key={user.id}>
              <Table.Td>{user.name}</Table.Td>
              <Table.Td>
                <Badge color={user.status === "active" ? "success" : "gray"}>
                  {user.status === "active" ? "활성" : "비활성"}
                </Badge>
              </Table.Td>
              <Table.Td>
                <Button size="xs" onClick={() => handleEdit(user)}>
                  수정
                </Button>
              </Table.Td>
            </Table.Tr>
          ))}
        </Table.Tbody>
      </Table>
      <Group justify="center" mt="md">
        <Pagination total={totalPages} value={currentPage} onChange={setCurrentPage} />
      </Group>
    </>
  );
}
```

### After (DataTable)

```tsx
import { useState } from "react";
import { DataTable, Badge, Button } from "@l-fin/ui-components";
import { createColumnHelper, type PaginationState } from "@tanstack/react-table";

interface User {
  id: string;
  name: string;
  status: "active" | "inactive";
}

const columnHelper = createColumnHelper<User>();

function UsersTable({ users, onEdit }: { users: User[]; onEdit: (user: User) => void }) {
  const [pagination, setPagination] = useState<PaginationState>({
    pageIndex: 0,
    pageSize: 10,
  });

  const columns = [
    columnHelper.accessor("name", {
      header: "이름",
    }),
    columnHelper.accessor("status", {
      header: "상태",
      cell: (info) => (
        <Badge color={info.getValue() === "active" ? "success" : "gray"}>
          {info.getValue() === "active" ? "활성" : "비활성"}
        </Badge>
      ),
    }),
    columnHelper.display({
      id: "actions",
      header: "액션",
      cell: ({ row }) => (
        <Button size="xs" onClick={() => onEdit(row.original)}>
          수정
        </Button>
      ),
    }),
  ];

  return (
    <DataTable
      data={users}
      columns={columns}
      enablePagination
      manualPagination
      pagination={pagination}
      onPaginationChange={setPagination}
      rowCount={users.length}
      pageSizeOptions={[10, 20, 50]}
      enableSorting={false}
      enableVirtualization={false}
      emptyState={<div>데이터가 없습니다</div>}
    />
  );
}
```

### Conversion Checklist (Basic Table → DataTable)

- [ ] `Table`, `Table.Thead`, `Table.Tbody`, `Table.Tr`, `Table.Th`, `Table.Td` → single `DataTable` component
- [ ] Manual `.map()` rendering → `columns` array definition
- [ ] `useState` pagination → `PaginationState` type
- [ ] Separate `<Pagination>` component → `enablePagination` + `manualPagination`
- [ ] Conditional empty state rendering → `emptyState` prop
- [ ] Manual slice/pagination logic → DataTable built-in features
- [ ] Type-safe column definition with `createColumnHelper<T>`

---

## TanStack Table → DataTable Migration

### Before

```tsx
import {
  useReactTable,
  getCoreRowModel,
  flexRender,
  createColumnHelper,
} from '@tanstack/react-table';
import {Table, Checkbox, Badge} from '@mantine/core';

type User = {
  id: string;
  name: string;
  email: string;
  status: 'active' | 'inactive';
};

const columnHelper = createColumnHelper<User>();

const columns = [
  columnHelper.display({
    id: 'select',
    header: ({table}) => (
      <Checkbox
        checked={table.getIsAllRowsSelected()}
        indeterminate={table.getIsSomeRowsSelected()}
        onChange={table.getToggleAllRowsSelectedHandler()}
      />
    ),
    cell: ({row}) => (
      <Checkbox checked={row.getIsSelected()} onChange={row.getToggleSelectedHandler()} />
    ),
  }),
  columnHelper.accessor('name', {
    header: '이름',
  }),
  columnHelper.accessor('email', {
    header: '이메일',
  }),
  columnHelper.accessor('status', {
    header: '상태',
    cell: (info) => (
      <Badge color={info.getValue() === 'active' ? 'green' : 'gray'}>
        {info.getValue() === 'active' ? '활성' : '비활성'}
      </Badge>
    ),
  }),
];

function UsersTable({users}) {
  const [rowSelection, setRowSelection] = useState({});
  const [sorting, setSorting] = useState([]);

  const table = useReactTable({
    data: users,
    columns,
    state: {rowSelection, sorting},
    onRowSelectionChange: setRowSelection,
    onSortingChange: setSorting,
    getCoreRowModel: getCoreRowModel(),
    getSortedRowModel: getSortedRowModel(),
    getPaginationRowModel: getPaginationRowModel(),
  });

  return (
    <Table>
      <Table.Thead>
        {table.getHeaderGroups().map((headerGroup) => (
          <Table.Tr key={headerGroup.id}>
            {headerGroup.headers.map((header) => (
              <Table.Th key={header.id} onClick={header.column.getToggleSortingHandler()}>
                {flexRender(header.column.columnDef.header, header.getContext())}
              </Table.Th>
            ))}
          </Table.Tr>
        ))}
      </Table.Thead>
      <Table.Tbody>
        {table.getRowModel().rows.map((row) => (
          <Table.Tr key={row.id}>
            {row.getVisibleCells().map((cell) => (
              <Table.Td key={cell.id}>
                {flexRender(cell.column.columnDef.cell, cell.getContext())}
              </Table.Td>
            ))}
          </Table.Tr>
        ))}
      </Table.Tbody>
    </Table>
  );
}
```

### After

```tsx
import {DataTable} from '@l-fin/ui-components';
import {createColumnHelper} from '@tanstack/react-table';

type User = {
  id: string;
  name: string;
  email: string;
  status: 'active' | 'inactive';
};

const columnHelper = createColumnHelper<User>();

// Remove checkbox column - DataTable handles it automatically
const columns = [
  columnHelper.accessor('name', {
    header: '이름',
  }),
  columnHelper.accessor('email', {
    header: '이메일',
  }),
  columnHelper.accessor('status', {
    header: '상태',
    cell: (info) => (
      <Badge color={info.getValue() === 'active' ? 'green' : 'gray'}>
        {info.getValue() === 'active' ? '활성' : '비활성'}
      </Badge>
    ),
  }),
];

function UsersTable({users}) {
  const [rowSelection, setRowSelection] = useState({});

  return (
    <DataTable
      data={users}
      columns={columns}
      enableRowSelection
      rowSelection={rowSelection}
      onRowSelectionChange={setRowSelection}
      enableSorting
      enablePagination
    />
  );
}
```

---

## Virtual Scrolling

### Before (react-virtual direct usage)

```tsx
import {useVirtualizer} from '@tanstack/react-virtual';

const parentRef = useRef(null);
const rowVirtualizer = useVirtualizer({
  count: table.getRowModel().rows.length,
  getScrollElement: () => parentRef.current,
  estimateSize: () => 48,
  overscan: 5,
});

return (
  <div ref={parentRef} style={{height: '500px', overflow: 'auto'}}>
    <div style={{height: `${rowVirtualizer.getTotalSize()}px`}}>
      {rowVirtualizer.getVirtualItems().map((virtualRow) => {
        const row = table.getRowModel().rows[virtualRow.index];
        return (
          <Table.Tr
            key={row.id}
            style={{
              transform: `translateY(${virtualRow.start}px)`,
              position: 'absolute',
            }}
          >
            {/* cells */}
          </Table.Tr>
        );
      })}
    </div>
  </div>
);
```

### After

```tsx
<DataTable
  data={largeDataset}
  columns={columns}
  enableVirtualization // default: true
  estimateSize={() => 48}
  overscan={10}
  height={500}
/>
```

---

## Row Actions

### Before

```tsx
const columns = [
  // ... other columns
  columnHelper.display({
    id: 'actions',
    header: '',
    cell: ({row}) => (
      <Menu>
        <Menu.Target>
          <ActionIcon>
            <IconDots />
          </ActionIcon>
        </Menu.Target>
        <Menu.Dropdown>
          <Menu.Item onClick={() => handleEdit(row.original)}>수정</Menu.Item>
          <Menu.Item color="red" onClick={() => handleDelete(row.original)}>
            삭제
          </Menu.Item>
        </Menu.Dropdown>
      </Menu>
    ),
  }),
];
```

### After

```tsx
<DataTable
  data={data}
  columns={columns} // remove actions column
  actions={[
    {
      label: '수정',
      icon: <IconEdit size={16} />,
      onClick: (row) => handleEdit(row),
    },
    {
      label: '삭제',
      icon: <IconTrash size={16} />,
      color: 'error',
      onClick: (row) => handleDelete(row),
    },
  ]}
/>
```

---

## Pagination Pattern Selection

When using external Pagination component, choose between two patterns.

### Pattern A: DataTable Built-in Pagination (Recommended)

Use DataTable's `enablePagination` to integrate pagination UI and logic.

```tsx
const [pagination, setPagination] = useState({pageIndex: 0, pageSize: 10});

const {data, isLoading} = useQuery({
  queryKey: ['users', pagination],
  queryFn: () =>
    fetchUsers({
      page: pagination.pageIndex + 1,
      size: pagination.pageSize,
    }),
});

return (
  <DataTable
    data={data?.items ?? []}
    columns={columns}
    loading={isLoading}
    enablePagination
    manualPagination
    rowCount={data?.totalCount ?? 0}
    pagination={pagination}
    onPaginationChange={setPagination}
    pageSizeOptions={[10, 25, 50, 100]}
  />
);
```

**Advantages:**

- Simplified code (no external Pagination component needed)
- Consistent UI (matches DataTable style)
- Built-in page size selection

#### Pattern A + URL Sync (with nuqs)

Use nuqs to sync pagination state with URL.

```tsx
import {useQueryStates, parseAsInteger} from 'nuqs';

const PaginationParams = {
  page: parseAsInteger.withDefault(1),
  size: parseAsInteger.withDefault(10),
};

function UsersTable() {
  const [{page, size}, setParams] = useQueryStates(PaginationParams);

  const pagination = useMemo(() => ({pageIndex: page - 1, pageSize: size}), [page, size]);

  const handlePaginationChange = (
    updater: PaginationState | ((old: PaginationState) => PaginationState)
  ) => {
    const newState = typeof updater === 'function' ? updater(pagination) : updater;
    setParams({
      page: newState.pageIndex + 1,
      size: newState.pageSize,
    });
  };

  const {data, isLoading} = useQuery({
    queryKey: ['users', page, size],
    queryFn: () => fetchUsers({page, size}),
  });

  return (
    <DataTable
      data={data?.items ?? []}
      columns={columns}
      loading={isLoading}
      enablePagination
      manualPagination
      rowCount={data?.totalCount ?? 0}
      pagination={pagination}
      onPaginationChange={handlePaginationChange}
      pageSizeOptions={[10, 25, 50, 100]}
    />
  );
}
```

#### Pattern A + URL Sync (with TanStack Router directly)

Use TanStack Router directly without nuqs.

```tsx
import {useNavigate, useSearch} from '@tanstack/react-router';

type SearchParams = Record<string, unknown>;

function UsersTable() {
  const search = useSearch({strict: false}) as SearchParams;
  const navigate = useNavigate();

  const page = Number(search.page) || 1;
  const size = Number(search.size) || 10;

  const pagination = useMemo(() => ({pageIndex: page - 1, pageSize: size}), [page, size]);

  const handlePaginationChange = (
    updater: PaginationState | ((old: PaginationState) => PaginationState)
  ) => {
    const newState = typeof updater === 'function' ? updater(pagination) : updater;
    void navigate({
      to: '.',
      search: {
        ...search,
        page: newState.pageIndex + 1,
        size: newState.pageSize,
      } as SearchParams,
      replace: true,
    });
  };

  const {data, isLoading} = useQuery({
    queryKey: ['users', page, size],
    queryFn: () => fetchUsers({page, size}),
  });

  return (
    <DataTable
      data={data?.items ?? []}
      columns={columns}
      loading={isLoading}
      enablePagination
      manualPagination
      rowCount={data?.totalCount ?? 0}
      pagination={pagination}
      onPaginationChange={handlePaginationChange}
      pageSizeOptions={[10, 25, 50, 100]}
    />
  );
}
```

### Pattern B: Keep External Pagination

Keep existing Mantine Pagination component. Recommended when URL params sync is needed.

```tsx
// URL params 사용 시 (TanStack Router / nuqs)
const {searchParams, updateFilter} = useSiteParams();

const {data, isLoading} = useSiteSearch();

return (
  <>
    <DataTable
      data={data?.items ?? []}
      columns={columns}
      loading={isLoading}
      // Don't use enablePagination
    />

    {/* External pagination */}
    <Group justify="center" mt={12}>
      <Pagination
        total={data?.totalPage ?? 0}
        value={searchParams.page}
        onChange={(page) => updateFilter({page})}
      />
    </Group>
  </>
);
```

**Advantages:**

- URL and pagination state sync
- Minimal code changes
- Custom pagination UI maintainable

### Pattern Comparison

| Item              | Pattern A (Built-in) | Pattern B (External) |
| ----------------- | -------------------- | -------------------- |
| Code amount       | Less                 | More                 |
| URL sync          | Needs implementation | Existing pattern     |
| UI customization  | DataTable style      | Flexible             |
| SizeSelect        | Built-in             | Separate impl        |
| Recommended       | New projects         | URL params usage     |

### BaseTable Wrapper Usage

```tsx
// Pattern A: Built-in pagination
<BaseTable
  data={data?.items ?? []}
  columns={columns}
  isLoading={isLoading}
  enablePagination
  totalCount={data?.totalCount}
  pagination={{ pageIndex: page - 1, pageSize: size }}
  onPaginationChange={(updater) => {
    const newState = typeof updater === 'function'
      ? updater({ pageIndex: page - 1, pageSize: size })
      : updater;
    updateFilter({ page: newState.pageIndex + 1, size: newState.pageSize });
  }}
/>

// Pattern B: External pagination (using footer)
<BaseTable
  data={data?.items ?? []}
  columns={columns}
  isLoading={isLoading}
  footer={
    <Pagination
      total={data?.totalPage ?? 0}
      value={searchParams.page}
      onChange={(page) => updateFilter({ page })}
    />
  }
/>
```

---

## Loading State

### Before

```tsx
if (isLoading) {
  return (
    <Table>
      <Table.Tbody>
        {Array.from({length: 5}).map((_, i) => (
          <Table.Tr key={i}>
            <Table.Td>
              <Skeleton height={20} />
            </Table.Td>
            <Table.Td>
              <Skeleton height={20} />
            </Table.Td>
            <Table.Td>
              <Skeleton height={20} />
            </Table.Td>
          </Table.Tr>
        ))}
      </Table.Tbody>
    </Table>
  );
}
```

### After

```tsx
<DataTable data={data} columns={columns} loading={isLoading} loadingRowCount={5} />
```

---

## Empty State

### Before

```tsx
if (data.length === 0) {
  return (
    <Center py="xl">
      <Stack align="center">
        <IconInbox size={48} color="gray" />
        <Text c="dimmed">데이터가 없습니다</Text>
        <Button onClick={handleCreate}>항목 추가</Button>
      </Stack>
    </Center>
  );
}
```

### After

```tsx
import {EmptyState} from '@l-fin/ui-components';

<DataTable
  data={data}
  columns={columns}
  emptyState={
    <EmptyState
      title="데이터가 없습니다"
      description="새로운 항목을 추가해보세요"
      action={<Button onClick={handleCreate}>항목 추가</Button>}
    />
  }
/>;
```

---

## Column Visibility

### Before

```tsx
const [columnVisibility, setColumnVisibility] = useState({
  createdAt: false,
  updatedAt: false,
});

const table = useReactTable({
  state: {columnVisibility},
  onColumnVisibilityChange: setColumnVisibility,
  // ...
});

// Manual column toggle UI
<Menu>
  {table.getAllColumns().map((column) => (
    <Menu.Item key={column.id}>
      <Checkbox
        checked={column.getIsVisible()}
        onChange={() => column.toggleVisibility()}
        label={column.columnDef.header}
      />
    </Menu.Item>
  ))}
</Menu>;
```

### After

```tsx
<DataTable
  data={data}
  columns={columns}
  enableColumnVisibility
  defaultHiddenColumnIds={['createdAt', 'updatedAt']}
/>
```

---

## Column Pinning

```tsx
<DataTable
  data={data}
  columns={columns}
  columnPinning={{
    left: ['id', 'name'], // pin to left
    right: ['actions'], // pin to right
  }}
/>
```

---

## Checklist

### Basic Migration

- [ ] `useReactTable` → `DataTable` component conversion
- [ ] Remove checkbox column → use `enableRowSelection`
- [ ] Remove sorting logic → use `enableSorting`
- [ ] Remove virtual scroll logic → use `enableVirtualization` (default)
- [ ] Remove actions column → use `actions` prop
- [ ] Remove loading skeleton → use `loading` prop
- [ ] Remove empty state handling → use `emptyState` prop
- [ ] Remove column visibility UI → use `enableColumnVisibility`

### Pagination (Pattern Selection)

- [ ] Choose pagination pattern: Pattern A (built-in) or Pattern B (external)
- [ ] Pattern A: Remove external Pagination component
- [ ] Pattern A: Remove SizeSelect component (use built-in)
- [ ] Pattern A: Set `enablePagination` + `manualPagination`
- [ ] Pattern A: Connect `rowCount`, `pagination`, `onPaginationChange` props
- [ ] Pattern B: Pass external Pagination via `footer` prop
