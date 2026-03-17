---
name: apply-composites
description: Apply Composite components (AppLayout, DataTable, FilterBuilder) to project. Use after running migrate to add advanced layout, table, and filter components.
---

# /apply-composites

Apply @l-fin/ui-components Composite components (AppLayout, DataTable, FilterBuilder) to the project.

## Output Language

**IMPORTANT**: All terminal output and user-facing messages MUST be in Korean (한국어).

## Prerequisites

> Run `/migrate` skill first to complete basic component migration to @l-fin/ui-components.

## Arguments

| Argument           | Description                              |
| ------------------ | ---------------------------------------- |
| (none)             | Apply all Composite components (default) |
| `--app-layout`     | Apply AppLayout component only           |
| `--datatable`      | Apply DataTable component only           |
| `--filter-builder` | Apply FilterBuilder component only       |

Multiple arguments can be combined: `/apply-composites --app-layout --filter-builder`

---

## Composite Components Overview

### AppLayout

Unified sidebar + header layout component.

- Resizable sidebar with localStorage persistence
- Nested navigation support
- Mobile responsive layout
- Dark mode toggle built-in

### DataTable

Full-featured data table based on TanStack Table.

- Virtual scrolling (large datasets)
- Sorting / Pagination / Filtering
- Row selection and actions
- Column pinning and visibility

### FilterBuilder

Declarative dynamic filter form generator.

- Multiple filter types (text, select, dateRange, etc.)
- Dynamic filter dependencies
- URL query parameter sync
- Seamless DataTable integration

---

## Reference Documentation

Read @l-fin/ui-components docs for component reference (use WebFetch):

| URL                                                                  | Description                           |
| -------------------------------------------------------------------- | ------------------------------------- |
| `https://admin-template.lpin.io/llms/layout/app-layout.md`           | AppLayout component API reference     |
| `https://admin-template.lpin.io/llms/layout/types.md`                | AppLayout type definitions            |
| `https://admin-template.lpin.io/llms/composites/datatable.md`        | DataTable component API reference     |
| `https://admin-template.lpin.io/llms/composites/filter-builder.md`   | FilterBuilder component API reference |
| `https://admin-template.lpin.io/llms/composites/types.md`            | DataTable, FilterBuilder types        |
| `https://admin-template.lpin.io/llms/patterns/crud.md`               | Complete CRUD page pattern            |
| `https://admin-template.lpin.io/llms/patterns/filter.md`             | FilterBuilder usage patterns          |
| `https://admin-template.lpin.io/llms/patterns/table.md`              | DataTable usage patterns              |

## Migration Guides

| Guide                                    | Description                                   |
| ---------------------------------------- | --------------------------------------------- |
| `references/app-layout-migration.md`     | AppShell → AppLayout migration                |
| `references/datatable-migration.md`      | TanStack Table → DataTable migration          |
| `references/filter-builder-migration.md` | Custom filter forms → FilterBuilder migration |

---

## Procedure

### Step 1: Identify Migration Targets

Detect files that match each component pattern.

#### AppLayout Targets

**Search patterns**:

```bash
# File name patterns
Glob pattern="src/**/*Layout*.tsx"
Glob pattern="src/**/*Sidebar*.tsx"
Glob pattern="src/**/*Navbar*.tsx"

# Code patterns
Grep pattern="AppShell" glob="*.{ts,tsx}" path="src/"
Grep pattern="NavLink.*leftSection" glob="*.{ts,tsx}" path="src/"
```

**Indicators**:

- `AppShell`, `AppShell.Header`, `AppShell.Navbar`, `AppShell.Main`
- Custom sidebar with `NavLink` components
- Manual mobile burger menu handling

#### DataTable Targets

**Search patterns**:

```bash
# Pattern A: Direct TanStack Table usage
Grep pattern="useReactTable" glob="*.{ts,tsx}" path="src/"
Grep pattern="getCoreRowModel" glob="*.{ts,tsx}" path="src/"
Grep pattern="getSortedRowModel|getPaginationRowModel" glob="*.{ts,tsx}" path="src/"

# Pattern B: Basic Mantine Table usage
Grep pattern="<Table>" glob="*.{ts,tsx}" path="src/"
Grep pattern="Table\.Thead|Table\.Tbody|Table\.Tr" glob="*.{ts,tsx}" path="src/"
Grep pattern="<Pagination" glob="*.{ts,tsx}" path="src/"
```

**Indicators**:

**Pattern A - Direct TanStack Table**:
- Direct `useReactTable`, `getCoreRowModel` usage
- Manual table rendering with `flexRender`
- Manual checkbox column for row selection

**Pattern B - Basic Mantine Table** (recommend upgrade to DataTable):
- `<Table>`, `<Table.Thead>`, `<Table.Tbody>`, `<Table.Tr>` usage
- Manual `.map()` rendering
- Separate `<Pagination>` component
- Manual pagination logic (`useState` for currentPage, perPage)
- Conditional empty state rendering (`if (data.length === 0)`)

#### FilterBuilder Targets

**Search patterns**:

```bash
# Multiple filter inputs in a form
Grep pattern="<form.*onSubmit" glob="*.{ts,tsx}" path="src/"
Grep pattern="(Select|TextInput|DatePickerInput).*label=" glob="*.{ts,tsx}" path="src/"
```

**Indicators**:

- Multiple `useState` for filter values
- Form with Select, TextInput, DatePickerInput combination
- Manual reset logic
- Manual search button handling

### Step 2: Load Migration Guides

Based on detected targets, read the corresponding migration guides:

- AppLayout: read [references/app-layout-migration.md](references/app-layout-migration.md)
- DataTable: read [references/datatable-migration.md](references/datatable-migration.md)
- FilterBuilder: read [references/filter-builder-migration.md](references/filter-builder-migration.md)

### Step 2.5: FilterBuilder URL Sync Pattern Selection

When migrating FilterBuilder with existing `nuqs` (useQueryStates) usage, ask user to choose URL sync pattern.

**Check nuqs usage**:

```bash
Grep pattern="from \"nuqs\"" glob="*.{ts,tsx}" path="src/"
Grep pattern="useQueryStates" glob="*.{ts,tsx}" path="src/"
```

**If nuqs is used, ask user with AskUserQuestion**:

```
FilterBuilder URL 동기화 패턴을 어떻게 적용할까요?

1. Pattern A (nuqs 유지) - 기존 nuqs 훅 유지
2. Pattern B (TanStack Router 직접 사용) [권장] - nuqs 제거
3. 혼합 사용 - 파일별로 선택적 적용
```

#### Pattern A: Keep nuqs

```tsx
// Keep existing nuqs hook
import {useQueryStates, parseAsString} from 'nuqs';

const SiteParams = {
  siteName: parseAsString.withDefault(''),
  sort: parseAsString.withDefault(''),
};

export const SiteSearchFilters = () => {
  const [searchParams, setSearchParams] = useQueryStates(SiteParams);

  const handleSearch = (values: Record<string, unknown>) => {
    setSearchParams({
      siteName: (values.siteName as string) ?? '',
      sort: (values.sort as string) ?? '',
    });
  };

  return (
    <FilterBuilder
      filters={filters}
      defaultValues={{siteName: searchParams.siteName, sort: searchParams.sort}}
      onSearch={handleSearch}
      onReset={() => setSearchParams({siteName: '', sort: ''})}
    />
  );
};
```

#### Pattern B: Use TanStack Router directly (recommended)

```tsx
// Remove nuqs, use TanStack Router directly
import {useNavigate, useSearch} from '@tanstack/react-router';

type SearchParams = Record<string, unknown>;

export const SiteSearchFilters = () => {
  const search = useSearch({strict: false}) as SearchParams;
  const navigate = useNavigate();

  const handleSearch = (values: Record<string, unknown>) => {
    void navigate({
      to: '.',
      search: {
        ...search,
        siteName: (values.siteName as string) || undefined,
        sort: (values.sort as string) || undefined,
        page: 1, // Reset to first page on search
      } as SearchParams,
      replace: true,
    });
  };

  const handleReset = () => {
    void navigate({
      to: '.',
      search: {size: search.size, page: 1} as SearchParams,
      replace: true,
    });
  };

  return (
    <FilterBuilder
      filters={filters}
      defaultValues={{
        siteName: (search.siteName as string) ?? '',
        sort: (search.sort as string) ?? '',
      }}
      onSearch={handleSearch}
      onReset={handleReset}
    />
  );
};
```

**Additional work for Pattern B**:

- Migrate related `use*Params.ts` hooks together
- Remove nuqs from package.json when all usages are removed

---

### Step 2.6: DataTable Pagination Pattern Selection

When migrating DataTable with existing external Pagination component, ask user to choose pagination pattern.

**Check external pagination usage**:

```bash
Grep pattern="<Pagination" glob="*.{ts,tsx}" path="src/"
Grep pattern="pagination.*onChange" glob="*.{ts,tsx}" path="src/"
```

**If external pagination is used, ask user with AskUserQuestion**:

```
DataTable 페이지네이션 패턴을 어떻게 적용할까요?

1. Pattern A: DataTable 내장 페이지네이션 사용 [권장]
2. Pattern B: 외부 페이지네이션 유지
```

#### Pattern A: DataTable built-in pagination (recommended)

```tsx
const [pagination, setPagination] = useState({pageIndex: 0, pageSize: 10});

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

#### Pattern B: Keep external pagination

```tsx
// When using BaseTable
<BaseTable
  data={data?.items ?? []}
  columns={columns}
  isLoading={isLoading}
  footer={
    <Group justify="center">
      <Pagination
        total={data?.totalPage ?? 0}
        value={searchParams.page}
        onChange={(page) => updateFilter({page})}
      />
    </Group>
  }
/>
```

**Additional work for Pattern A**:

- Remove external Pagination component
- Remove SizeSelect component (use built-in)
- Update URL in `onPaginationChange` for URL params sync

---

### Step 2.7: Migration Complexity Evaluation and User Confirmation

**CRITICAL**: Do not arbitrarily defer or skip migration. MUST use `AskUserQuestion` tool to confirm with user.

#### DataTable Migration Complexity Evaluation

After analyzing existing table implementation, ask user in these situations:

```
DataTable 마이그레이션을 어떻게 진행할까요?

현재 상황: [분석 결과]

1. DataTable로 마이그레이션 진행 [권장]
2. 기존 구조 유지
3. 부분 마이그레이션
```

**Migration target types**:

| Type | Detection Pattern | Complexity |
|------|-------------------|------------|
| TanStack Table direct | `useReactTable`, `getCoreRowModel` | Medium |
| Basic Mantine Table | `<Table>`, `Table.Thead`, `Table.Tbody` | Low |
| External Pagination | `<Pagination>`, `useState` for page | Low |

#### AppLayout Migration Complexity Evaluation

After analyzing existing layout implementation, ask user in these situations:

```
AppLayout 마이그레이션을 어떻게 진행할까요?

현재 상황: [분석 결과]

1. AppLayout으로 마이그레이션 진행 [권장]
2. AppShell 유지
3. 부분 마이그레이션
```

**Cautions**:

- NEVER defer migration without user confirmation
- User may want to proceed even if complexity seems high
- Respect user's choice and proceed accordingly

---

### Step 3: Execute Migration

Follow the migration guide for each component type.

**Migration Order** (recommended):

1. **FilterBuilder** - Simplest, least dependencies
2. **DataTable** - May depend on FilterBuilder
3. **AppLayout** - Structural change, do last

**Per-file process**:

1. Read the original file
2. Identify migration patterns
3. Apply transformations following the guide
4. Verify no functionality is lost

### Step 4: Verification

Run verification following [../migrate/references/common/verification.md](../migrate/references/common/verification.md).

### Step 5: Generate Result File

Generate a result file using the template in [references/result-file-template.md](references/result-file-template.md).

---

## Notes

1. **Incremental migration recommended**: Migrate one component at a time rather than all at once
2. **Preserve business logic**: Keep existing state management and business logic intact
3. **Style customization**: Use component props for style adjustments
4. **Test thoroughly**: Verify UI after each component migration
5. **CRITICAL - User confirmation required**: Migration defer/skip decisions MUST be confirmed with user via `AskUserQuestion` tool. Do not arbitrarily defer.
