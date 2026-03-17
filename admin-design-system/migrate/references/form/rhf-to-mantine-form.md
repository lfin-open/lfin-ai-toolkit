# React Hook Form → @mantine/form

Migration guide from react-hook-form + Zod to @mantine/form.

## Overview

| Item          | Value                              |
| ------------- | ---------------------------------- |
| Difficulty    | Medium                             |
| Automatable   | 50~60%                             |
| Main work     | Hook API changes + validation      |

---

## Core Changes

| react-hook-form    | @mantine/form                             |
| ------------------ | ----------------------------------------- |
| `useForm()`        | `useForm()`                               |
| `defaultValues`    | `initialValues`                           |
| `handleSubmit`     | `onSubmit`                                |
| `register`         | `getInputProps`                           |
| `formState.errors` | Auto-display (built into Input)           |
| `zodResolver`      | `zodResolver` (mantine-form-zod-resolver) |

---

## Basic Conversion

### Before (React Hook Form + Zod)

```tsx
import {useForm} from 'react-hook-form';
import {zodResolver} from '@hookform/resolvers/zod';
import {z} from 'zod';

const schema = z.object({
  name: z.string().min(1, '이름을 입력하세요'),
  email: z.string().email('올바른 이메일을 입력하세요'),
});

type FormData = z.infer<typeof schema>;

function MyForm() {
  const {
    register,
    handleSubmit,
    formState: {errors, isSubmitting},
  } = useForm<FormData>({
    resolver: zodResolver(schema),
    defaultValues: {name: '', email: ''},
  });

  const onSubmit = async (data: FormData) => {
    await saveData(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label>이름</label>
        <input {...register('name')} />
        {errors.name && <span>{errors.name.message}</span>}
      </div>
      <div>
        <label>이메일</label>
        <input type="email" {...register('email')} />
        {errors.email && <span>{errors.email.message}</span>}
      </div>
      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? '저장 중...' : '저장'}
      </button>
    </form>
  );
}
```

### After (@mantine/form)

```tsx
import {useForm} from '@mantine/form';
import {zodResolver} from 'mantine-form-zod-resolver';
import {TextInput, Button} from '@l-fin/ui-components';
import {z} from 'zod';

const schema = z.object({
  name: z.string().min(1, '이름을 입력하세요'),
  email: z.string().email('올바른 이메일을 입력하세요'),
});

type FormData = z.infer<typeof schema>;

function MyForm() {
  const form = useForm<FormData>({
    mode: 'uncontrolled',
    initialValues: {name: '', email: ''},
    validate: zodResolver(schema),
  });

  const [loading, setLoading] = useState(false);

  const handleSubmit = async (data: FormData) => {
    setLoading(true);
    try {
      await saveData(data);
    } finally {
      setLoading(false);
    }
  };

  return (
    <form onSubmit={form.onSubmit(handleSubmit)}>
      <TextInput label="이름" key={form.key('name')} {...form.getInputProps('name')} />
      <TextInput
        label="이메일"
        type="email"
        key={form.key('email')}
        {...form.getInputProps('email')}
      />
      <Button type="submit" loading={loading}>
        저장
      </Button>
    </form>
  );
}
```

---

## Key API Conversion

### useForm Options

```tsx
// react-hook-form
useForm({
  resolver: zodResolver(schema),
  defaultValues: {name: '', email: ''},
  mode: 'onChange',
});

// @mantine/form
useForm({
  validate: zodResolver(schema),
  initialValues: {name: '', email: ''},
  validateInputOnChange: true,
  mode: 'uncontrolled', // recommended for performance
});
```

### Input Binding

```tsx
// react-hook-form
<input {...register('name')} />

// @mantine/form
<TextInput
  key={form.key('name')}
  {...form.getInputProps('name')}
/>
```

**Important**: `key={form.key('fieldName')}` required when using `mode: 'uncontrolled'`

### Error Display

```tsx
// react-hook-form - manual display
<input {...register('name')} />;
{errors.name && <span className="error">{errors.name.message}</span>;}

// @mantine/form - auto display
<TextInput {...form.getInputProps('name')} />;
// Error message automatically shown below input
```

### Form Submit

```tsx
// react-hook-form
<form onSubmit={handleSubmit(onSubmit)}>

// @mantine/form
<form onSubmit={form.onSubmit(onSubmit)}>
```

---

## Zod Integration

### Package Installation

```bash
pnpm add mantine-form-zod-resolver
```

### Usage

```tsx
import {zodResolver} from 'mantine-form-zod-resolver';
import {z} from 'zod';

const schema = z.object({
  email: z.string().email('올바른 이메일 형식이 아닙니다'),
  password: z.string().min(8, '비밀번호는 8자 이상이어야 합니다'),
});

const form = useForm({
  initialValues: {email: '', password: ''},
  validate: zodResolver(schema),
});
```

---

## Manual Validation (Without Zod)

```tsx
const form = useForm({
  initialValues: {email: '', age: 0},
  validate: {
    email: (value) => {
      if (!value) return '이메일을 입력하세요';
      if (!/^\S+@\S+$/.test(value)) return '올바른 이메일 형식이 아닙니다';
      return null;
    },
    age: (value) => (value < 18 ? '18세 이상이어야 합니다' : null),
  },
});
```

---

## Advanced Features

### Field Array (useFieldArray → form.insertListItem)

```tsx
// react-hook-form
const {fields, append, remove} = useFieldArray({
  control,
  name: 'items',
});

fields.map((field, index) => <input key={field.id} {...register(`items.${index}.name`)} />);

// @mantine/form
const form = useForm({
  initialValues: {
    items: [{name: ''}],
  },
});

form.values.items.map((_, index) => (
  <TextInput key={form.key(`items.${index}.name`)} {...form.getInputProps(`items.${index}.name`)} />
));

// Add/Remove
form.insertListItem('items', {name: ''});
form.removeListItem('items', index);
```

### watch → form.values

```tsx
// react-hook-form
const watchedValue = watch('fieldName');

// @mantine/form
const watchedValue = form.values.fieldName;
// or (in uncontrolled mode)
const watchedValue = form.getValues().fieldName;
```

### setValue → form.setFieldValue

```tsx
// react-hook-form
setValue('name', 'John');

// @mantine/form
form.setFieldValue('name', 'John');
```

### reset → form.reset

```tsx
// react-hook-form
reset();
reset({name: '', email: ''});

// @mantine/form
form.reset();
form.setInitialValues({name: '', email: ''});
```

### trigger → form.validate

```tsx
// react-hook-form
await trigger('email');
await trigger();

// @mantine/form
form.validateField('email');
form.validate();
```

---

## Search Commands

```bash
# Find react-hook-form usage
Grep pattern="from ['\"]react-hook-form['\"]" glob="*.{ts,tsx}" path="src/"

# Find useForm usage
Grep pattern="useForm\\(" glob="*.{ts,tsx}" path="src/"

# Find register usage
Grep pattern="\\.register\\(" glob="*.{ts,tsx}" path="src/"

# Find handleSubmit usage
Grep pattern="handleSubmit\\(" glob="*.{ts,tsx}" path="src/"
```

---

## Removal Targets

Remove after migration:

1. **Packages**:
   - `react-hook-form`
   - `@hookform/resolvers`

```bash
pnpm remove react-hook-form @hookform/resolvers
pnpm add mantine-form-zod-resolver  # if using Zod
```

---

## Notes

1. **mode: 'uncontrolled'**: Recommended for performance, requires `key={form.key()}`
2. **Auto error display**: Mantine Input components handle this automatically
3. **zodResolver import**: From `mantine-form-zod-resolver` (note package name)
4. **Nested objects**: Access via `form.getInputProps('address.city')` format
5. **Type inference**: `z.infer<typeof schema>` works the same way
