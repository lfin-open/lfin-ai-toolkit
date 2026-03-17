# FilterBuilder Migration Guide

Migrate custom search/filter forms to the `FilterBuilder` component.

## Migration Target Identification

Files are migration targets if they contain:

```tsx
// Before: Custom filter form
<form onSubmit={handleSearch}>
  <Group>
    <Select label="상태" value={status} onChange={setStatus} data={statusOptions} />
    <TextInput label="검색어" value={keyword} onChange={(e) => setKeyword(e.target.value)} />
    <DatePickerInput type="range" label="기간" value={dateRange} onChange={setDateRange} />
    <Button type="submit">검색</Button>
    <Button variant="default" onClick={handleReset}>
      초기화
    </Button>
  </Group>
</form>
```

---

## Basic Migration

### Before

```tsx
import {Select, TextInput, Button, Group} from '@mantine/core';
import {DatePickerInput} from '@mantine/dates';
import {useState, FormEvent} from 'react';

function SearchFilters({onSearch}) {
  const [status, setStatus] = useState('');
  const [keyword, setKeyword] = useState('');
  const [dateRange, setDateRange] = useState([null, null]);

  const handleSubmit = (e: FormEvent) => {
    e.preventDefault();
    onSearch({status, keyword, dateRange});
  };

  const handleReset = () => {
    setStatus('');
    setKeyword('');
    setDateRange([null, null]);
    onSearch({});
  };

  return (
    <form onSubmit={handleSubmit}>
      <Group>
        <Select
          label="상태"
          placeholder="선택"
          value={status}
          onChange={(value) => setStatus(value ?? '')}
          data={[
            {value: 'active', label: '활성'},
            {value: 'inactive', label: '비활성'},
          ]}
          clearable
        />
        <TextInput
          label="이름"
          placeholder="이름으로 검색..."
          value={keyword}
          onChange={(e) => setKeyword(e.target.value)}
        />
        <DatePickerInput
          type="range"
          label="생성일"
          placeholder="기간 선택"
          value={dateRange}
          onChange={setDateRange}
        />
        <Button type="submit">검색</Button>
        <Button variant="default" onClick={handleReset}>
          초기화
        </Button>
      </Group>
    </form>
  );
}
```

### After

```tsx
import {FilterBuilder, defineFilters} from '@l-fin/ui-components';

const filters = defineFilters([
  {
    name: 'status',
    type: 'select',
    label: '상태',
    placeholder: '선택',
    options: [
      {value: 'active', label: '활성'},
      {value: 'inactive', label: '비활성'},
    ],
  },
  {
    name: 'keyword',
    type: 'text',
    label: '이름',
    placeholder: '이름으로 검색...',
  },
  {
    name: 'createdAt',
    type: 'dateRange',
    label: '생성일',
    placeholder: '기간 선택',
  },
]);

function SearchFilters({onSearch}) {
  return (
    <FilterBuilder filters={filters} onSearch={onSearch} searchLabel="검색" resetLabel="초기화" />
  );
}
```

---

## Filter Type Migration

### Text Filter

```tsx
// Before
<TextInput
  label="검색어"
  value={value}
  onChange={(e) => setValue(e.target.value)}
  placeholder="검색..."
/>

// After (in defineFilters)
{
  name: "keyword",
  type: "text",
  label: "검색어",
  placeholder: "검색...",
  debounceMs: 300,  // optional
}
```

### Select Filter

```tsx
// Before
<Select
  label="카테고리"
  value={value}
  onChange={setValue}
  data={categoryOptions}
  clearable
/>

// After
{
  name: "category",
  type: "select",
  label: "카테고리",
  options: categoryOptions,  // { value, label }[]
}
```

### MultiSelect Filter

```tsx
// Before
<MultiSelect
  label="태그"
  value={tags}
  onChange={setTags}
  data={tagOptions}
/>

// After
{
  name: "tags",
  type: "multiSelect",
  label: "태그",
  options: tagOptions,
  virtualized: true,           // for large option lists
  virtualizeThreshold: 100,
}
```

### DateRange Filter

```tsx
// Before
<DatePickerInput
  type="range"
  label="기간"
  value={dateRange}
  onChange={setDateRange}
/>

// After
{
  name: "period",
  type: "dateRange",
  label: "기간",
}
// Result: [Date | null, Date | null]
```

### Checkbox/Switch Filter

```tsx
// Before
<Checkbox
  label="활성화된 항목만"
  checked={isActive}
  onChange={(e) => setIsActive(e.target.checked)}
/>

// After
{
  name: "isActive",
  type: "checkbox",  // or "switch"
  label: "활성화된 항목만",
}
```

### Radio Filter

```tsx
// Before
<Radio.Group label="정렬" value={sort} onChange={setSort}>
  <Radio value="asc" label="오름차순" />
  <Radio value="desc" label="내림차순" />
</Radio.Group>

// After
{
  name: "sortOrder",
  type: "radio",
  label: "정렬",
  options: [
    { value: "asc", label: "오름차순" },
    { value: "desc", label: "내림차순" },
  ],
}
```

---

## Dynamic Filter Migration

### Conditional Visibility

```tsx
// Before
{showDiscountField && (
  <Select label="할인율" ... />
)}

// After
{
  name: "discountRate",
  type: "select",
  label: "할인율",
  options: discountOptions,
  visible: (values) => values.hasDiscount === true,
}
```

### Dependent Filters

```tsx
// Before
const [category, setCategory] = useState("");
const [subcategory, setSubcategory] = useState("");

const subcategoryOptions = useMemo(() => {
  return getSubcategories(category);
}, [category]);

useEffect(() => {
  setSubcategory("");  // reset on category change
}, [category]);

<Select
  label="카테고리"
  value={category}
  onChange={setCategory}
  data={categoryOptions}
/>
<Select
  label="하위 카테고리"
  value={subcategory}
  onChange={setSubcategory}
  data={subcategoryOptions}
  disabled={!category}
/>

// After
const filters = defineFilters([
  {
    name: "category",
    type: "select",
    label: "카테고리",
    options: categoryOptions,
  },
  {
    name: "subcategory",
    type: "select",
    label: "하위 카테고리",
    dependsOn: "category",
    resetOnChange: ["category"],
    options: (values) => getSubcategories(values.category),
    disabled: (values) => !values.category,
  },
]);
```

### Disabled Condition

```tsx
// Before
<TextInput
  label="상세 검색"
  disabled={!keyword}
  ...
/>

// After
{
  name: "detail",
  type: "text",
  label: "상세 검색",
  disabled: (values) => !values.keyword,
}
```

---

## Layout Migration

### Inline Layout (default)

```tsx
// Before
<Group>
  <Select ... />
  <TextInput ... />
  <DatePickerInput ... />
</Group>

// After
<FilterBuilder
  filters={filters}
  layout="inline"  // default
/>
```

### Grid Layout

```tsx
// Before
<SimpleGrid cols={3}>
  <Select ... />
  <TextInput ... />
  <DatePickerInput ... />
</SimpleGrid>

// After
const filters = defineFilters([
  { name: "status", type: "select", label: "상태", span: 4 },
  { name: "keyword", type: "text", label: "검색어", span: 4 },
  { name: "period", type: "dateRange", label: "기간", span: 4 },
]);

<FilterBuilder
  filters={filters}
  layout="grid"
/>
```

### Width Control

```tsx
// Width control in inline layout
{
  name: "keyword",
  type: "text",
  label: "검색어",
  width: "lg",  // 'xs' | 'sm' | 'md' | 'lg' | 'xl' | 'auto'
}

// Span control in grid layout
{
  name: "keyword",
  type: "text",
  label: "검색어",
  span: 6,           // PC: 6/12 columns
  tabletSpan: 3,     // Tablet: 3/6 columns
  mobileSpan: 4,     // Mobile: full width
}
```

---

## URL Sync Migration

When using existing `nuqs`, choose between two patterns.

### Pattern A: Keep nuqs

Keep existing nuqs hook and integrate with FilterBuilder.

```tsx
import {useQueryStates, parseAsString} from 'nuqs';
import {FilterBuilder, type FilterFieldConfig} from '@l-fin/ui-components';

const SiteParams = {
  siteName: parseAsString.withDefault(''),
  sort: parseAsString.withDefault(''),
};

export const SiteSearchFilters = () => {
  const [searchParams, setSearchParams] = useQueryStates(SiteParams);

  const filters: FilterFieldConfig[] = [
    {name: 'siteName', type: 'text', label: 'SITE 이름'},
    {name: 'sort', type: 'select', label: '정렬', options: sortOptions},
  ];

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

### Pattern B: Use TanStack Router Directly (Recommended)

Remove nuqs and use TanStack Router directly.

```tsx
import {FilterBuilder, type FilterFieldConfig} from '@l-fin/ui-components';
import {useNavigate, useSearch} from '@tanstack/react-router';

type SearchParams = Record<string, unknown>;

export const SiteSearchFilters = () => {
  const search = useSearch({strict: false}) as SearchParams;
  const navigate = useNavigate();

  const filters: FilterFieldConfig[] = [
    {name: 'siteName', type: 'text', label: 'SITE 이름'},
    {name: 'sort', type: 'select', label: '정렬', options: sortOptions},
  ];

  const handleSearch = (values: Record<string, unknown>) => {
    void navigate({
      to: '.',
      search: {
        ...search,
        siteName: (values.siteName as string) || undefined,
        sort: (values.sort as string) || undefined,
        page: 1, // 검색 시 첫 페이지로
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

**When choosing Pattern B, migrate related hooks together:**

```tsx
// Before: useSiteParams.ts (nuqs)
import {parseAsInteger, parseAsString, useQueryStates} from 'nuqs';

export const useSiteParams = () => {
  const [searchParams, setSearchParams] = useQueryStates(SiteParams);
  // ...
};

// After: useSiteParams.ts (TanStack Router)
import {useNavigate, useSearch} from '@tanstack/react-router';

type SearchParams = Record<string, unknown>;

export const useSiteParams = () => {
  const search = useSearch({strict: false}) as SearchParams;
  const navigate = useNavigate();

  const searchParams = useMemo(
    () => ({
      page: Number(search.page) || 1,
      size: Number(search.size) || 30,
      siteName: (search.siteName as string) ?? '',
      sort: (search.sort as string) ?? '',
    }),
    [search.page, search.size, search.siteName, search.sort]
  );

  const updateFilter = useCallback(
    (updates) => {
      void navigate({
        to: '.',
        search: {...search, ...updates} as SearchParams,
        replace: true,
      });
    },
    [navigate, search]
  );

  return {searchParams, updateFilter};
};
```

### Pattern Comparison

| Item               | Pattern A (nuqs)      | Pattern B (TanStack Router)           |
| ------------------ | --------------------- | ------------------------------------- |
| Dependency         | Requires nuqs package | TanStack Router only                  |
| Type safety        | Schema-based parsing  | Manual type casting                   |
| Code complexity    | Separate hook file    | Direct handling in component          |
| Param conflicts    | None                  | None (uses spread)                    |
| Recommended        | When using nuqs       | New projects or migration             |

### FilterBuilder Built-in syncWithUrl

**Caution**: `syncWithUrl={true}` replaces entire search params.
Other URL params like page/size may be lost, so Pattern A or B is recommended.

```tsx
// Use only when there are no other URL params
<FilterBuilder filters={filters} onSearch={handleSearch} syncWithUrl={true} />
```

---

## Auto Search

### Before

```tsx
useEffect(() => {
  const timer = setTimeout(() => {
    onSearch({status, keyword});
  }, 500);
  return () => clearTimeout(timer);
}, [status, keyword]);
```

### After

```tsx
<FilterBuilder
  filters={filters}
  onSearch={handleSearch}
  autoSearch={true}
  debounceMs={500}
  hideSearchButton={true}
/>
```

---

## DataTable Integration

### Before

```tsx
function DataPage() {
  const [filters, setFilters] = useState({});
  const [columnFilters, setColumnFilters] = useState([]);

  const handleSearch = (values) => {
    setFilters(values);
    // Manual column filter conversion
    const newFilters = Object.entries(values)
      .filter(([_, v]) => v != null && v !== '')
      .map(([id, value]) => ({id, value}));
    setColumnFilters(newFilters);
  };

  return (
    <Stack>
      <SearchFilters onSearch={handleSearch} />
      <MyTable columnFilters={columnFilters} />
    </Stack>
  );
}
```

### After

```tsx
import {FilterBuilder, DataTable, filterValuesToColumnFilters} from '@l-fin/ui-components';

function DataPage() {
  const [columnFilters, setColumnFilters] = useState([]);

  const handleSearch = (values) => {
    const filters = filterValuesToColumnFilters(values);
    setColumnFilters(filters);
  };

  return (
    <Stack>
      <FilterBuilder filters={filterConfig} onSearch={handleSearch} />
      <DataTable
        data={data}
        columns={columns}
        enableColumnFilters
        columnFilters={columnFilters}
        onColumnFiltersChange={setColumnFilters}
      />
    </Stack>
  );
}
```

---

## Custom Filter Component

```tsx
// When a special filter is needed
{
  name: "color",
  type: "custom",
  label: "색상",
  render: ({ value, onChange }) => (
    <ColorPicker value={value} onChange={onChange} />
  ),
}
```

---

## Checklist

### Basic Migration

- [ ] Remove individual state variables → convert to `FilterFieldConfig[]` array
- [ ] Remove `form onSubmit` → use `onSearch` prop
- [ ] Remove reset logic → handled by `onReset` prop
- [ ] Remove layout components (`Group`, `SimpleGrid`) → use `layout` prop
- [ ] Convert conditional rendering → use `visible` function
- [ ] Convert dependent options logic → use `dependsOn`, `resetOnChange`
- [ ] Convert disabled conditions → use `disabled` function
- [ ] Remove debounce logic → use `debounceMs` prop
- [ ] Use `filterValuesToColumnFilters` utility for DataTable integration

### URL Sync (when using nuqs)

- [ ] Choose URL pattern: Pattern A (keep nuqs) or Pattern B (TanStack Router)
- [ ] If Pattern B: Migrate related `use*Params.ts` hooks together
- [ ] If Pattern B: Remove nuqs imports → add TanStack Router imports
- [ ] Set `defaultValues` to read from URL params
- [ ] Implement URL update logic in `handleSearch`
- [ ] Implement URL reset logic in `handleReset`
