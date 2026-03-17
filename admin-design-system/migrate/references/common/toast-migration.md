# Toast Migration Guide

Migration guide from various Toast/Notification libraries to @mantine/notifications.

## Overview

@l-fin/ui-components ThemeProvider includes @mantine/notifications built-in.
Remove existing Toast libraries and use `notifications.show()`.

---

## Sonner → @mantine/notifications

### Import Change

```tsx
// Before
import {toast} from 'sonner';

// After
import {notifications} from '@mantine/notifications';
```

### Basic Usage

```tsx
// Before (Sonner)
toast('메시지');
toast.success('성공!');
toast.error('에러 발생');
toast.loading('로딩 중...');

// After (@mantine/notifications)
notifications.show({message: '메시지'});
notifications.show({message: '성공!', color: 'green'});
notifications.show({message: '에러 발생', color: 'red'});
notifications.show({message: '로딩 중...', loading: true});
```

### Advanced Options

```tsx
// Before (Sonner)
toast.success('저장 완료', {
  description: '데이터가 성공적으로 저장되었습니다.',
  duration: 5000,
});

// After (@mantine/notifications)
notifications.show({
  title: '저장 완료',
  message: '데이터가 성공적으로 저장되었습니다.',
  color: 'green',
  autoClose: 5000,
});
```

### Promise Handling

```tsx
// Before (Sonner)
toast.promise(saveData(), {
  loading: '저장 중...',
  success: '저장 완료!',
  error: '저장 실패',
});

// After (@mantine/notifications)
const id = notifications.show({
  message: '저장 중...',
  loading: true,
  autoClose: false,
});

try {
  await saveData();
  notifications.update({
    id,
    message: '저장 완료!',
    color: 'green',
    loading: false,
    autoClose: 3000,
  });
} catch {
  notifications.update({
    id,
    message: '저장 실패',
    color: 'red',
    loading: false,
    autoClose: 3000,
  });
}
```

---

## react-toastify → @mantine/notifications

### Import Change

```tsx
// Before
import {toast, ToastContainer} from 'react-toastify';
import 'react-toastify/dist/ReactToastify.css';

// After
import {notifications} from '@mantine/notifications';
// Remove ToastContainer (included in ThemeProvider)
// Remove CSS import
```

### Remove ToastContainer

```tsx
// Before
function App() {
  return (
    <>
      <ToastContainer />
      <YourApp />
    </>
  );
}

// After - ThemeProvider includes Notifications
function App() {
  return (
    <ThemeProvider>
      <YourApp />
    </ThemeProvider>
  );
}
```

### Usage Conversion

```tsx
// Before (react-toastify)
toast('기본 메시지');
toast.success('성공!');
toast.error('에러!');
toast.info('정보');
toast.warning('경고');

// After (@mantine/notifications)
notifications.show({message: '기본 메시지'});
notifications.show({message: '성공!', color: 'green'});
notifications.show({message: '에러!', color: 'red'});
notifications.show({message: '정보', color: 'blue'});
notifications.show({message: '경고', color: 'yellow'});
```

---

## react-hot-toast → @mantine/notifications

### Import Change

```tsx
// Before
import toast, {Toaster} from 'react-hot-toast';

// After
import {notifications} from '@mantine/notifications';
// Remove Toaster (included in ThemeProvider)
```

### Remove Toaster

```tsx
// Before
function App() {
  return (
    <>
      <Toaster position="top-right" />
      <YourApp />
    </>
  );
}

// After
function App() {
  return (
    <ThemeProvider>
      <YourApp />
    </ThemeProvider>
  );
}
```

### Usage Conversion

```tsx
// Before (react-hot-toast)
toast('Hello World');
toast.success('성공!');
toast.error('에러!');
toast.loading('로딩 중...');

// After (@mantine/notifications)
notifications.show({message: 'Hello World'});
notifications.show({message: '성공!', color: 'green'});
notifications.show({message: '에러!', color: 'red'});
notifications.show({message: '로딩 중...', loading: true});
```

---

## @mantine/notifications API

### Basic Methods

```tsx
import {notifications} from '@mantine/notifications';

// Show notification
notifications.show({
  title: '제목',
  message: '메시지',
  color: 'blue', // 'green' | 'red' | 'yellow' | 'blue' | ...
  autoClose: 5000, // ms or false
  withCloseButton: true,
  loading: false,
});

// Update notification
notifications.update({
  id: 'notification-id',
  title: '업데이트된 제목',
  message: '업데이트된 메시지',
});

// Hide notification
notifications.hide('notification-id');

// Remove all notifications
notifications.clean();
```

### Convenience Pattern

```tsx
// Success notification
const showSuccess = (message: string) => {
  notifications.show({
    title: '성공',
    message,
    color: 'green',
  });
};

// Error notification
const showError = (message: string) => {
  notifications.show({
    title: '에러',
    message,
    color: 'red',
  });
};

// Usage
showSuccess('저장되었습니다');
showError('저장에 실패했습니다');
```

---

## Search Commands

```bash
# Find sonner usage
Grep pattern="from ['\"]sonner['\"]" glob="*.{ts,tsx}" path="src/"

# Find react-toastify usage
Grep pattern="from ['\"]react-toastify['\"]" glob="*.{ts,tsx}" path="src/"

# Find react-hot-toast usage
Grep pattern="from ['\"]react-hot-toast['\"]" glob="*.{ts,tsx}" path="src/"

# Find toast calls
Grep pattern="toast\\." glob="*.{ts,tsx}" path="src/"
```

---

## Removal Targets

Remove after migration:

### Packages

```bash
# Remove only what's being used
pnpm remove sonner
pnpm remove react-toastify
pnpm remove react-hot-toast
```

### Components

```tsx
// Remove
<Toaster />          // react-hot-toast
<ToastContainer />   // react-toastify
<Sonner />           // sonner (Toaster)
```

### CSS import

```tsx
// Remove
import 'react-toastify/dist/ReactToastify.css';
```

---

## Notes

1. **ThemeProvider required**: `notifications.show()` only works inside ThemeProvider
2. **Remove Notifications component**: Included in ThemeProvider, no separate addition needed
3. **color values**: Use Mantine color names (`'green'`, `'red'`, `'blue'`, etc.)
4. **autoClose**: In ms, set to `false` to disable auto-close
