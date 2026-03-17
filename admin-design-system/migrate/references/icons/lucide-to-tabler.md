# Lucide React → Tabler Icons

Migration guide from lucide-react to @tabler/icons-react.

## Overview

| Item          | Value                          |
| ------------- | ------------------------------ |
| Difficulty    | Low                            |
| Automatable   | 80~90%                         |
| Main work     | Import changes + name mapping  |

---

## Core Changes

| lucide-react  | @tabler/icons-react     |
| ------------- | ----------------------- |
| `<Monitor />` | `<IconDeviceDesktop />` |
| `strokeWidth` | `stroke`                |
| PascalCase    | Icon + PascalCase       |

---

## Icon Name Mapping

### Common Icons

| Lucide         | Tabler              | Description       |
| -------------- | ------------------- | ----------------- |
| `Monitor`      | `IconDeviceDesktop` | Desktop/Monitor   |
| `Plus`         | `IconPlus`          | Add               |
| `Minus`        | `IconMinus`         | Remove            |
| `Search`       | `IconSearch`        | Search            |
| `Pencil`       | `IconPencil`        | Edit              |
| `Edit`         | `IconEdit`          | Edit              |
| `Copy`         | `IconCopy`          | Copy              |
| `Trash2`       | `IconTrash`         | Delete            |
| `Settings`     | `IconSettings`      | Settings          |
| `ExternalLink` | `IconExternalLink`  | External link     |
| `LogOut`       | `IconLogout`        | Logout            |
| `LogIn`        | `IconLogin`         | Login             |
| `User`         | `IconUser`          | User              |
| `Users`        | `IconUsers`         | Users             |

### Layout/Navigation

| Lucide            | Tabler                | Description       |
| ----------------- | --------------------- | ----------------- |
| `LayoutGrid`      | `IconLayoutGrid`      | Grid layout       |
| `LayoutDashboard` | `IconLayoutDashboard` | Dashboard         |
| `Menu`            | `IconMenu2`           | Menu              |
| `ChevronRight`    | `IconChevronRight`    | Right arrow       |
| `ChevronLeft`     | `IconChevronLeft`     | Left arrow        |
| `ChevronDown`     | `IconChevronDown`     | Down arrow        |
| `ChevronUp`       | `IconChevronUp`       | Up arrow          |
| `ArrowLeft`       | `IconArrowLeft`       | Left arrow        |
| `ArrowRight`      | `IconArrowRight`      | Right arrow       |
| `ArrowLeftRight`  | `IconArrowsLeftRight` | Left-right arrow  |
| `Home`            | `IconHome`            | Home              |

### Media/Files

| Lucide                | Tabler         | Description  |
| --------------------- | -------------- | ------------ |
| `Image` / `ImageIcon` | `IconPhoto`    | Image        |
| `Video`               | `IconVideo`    | Video        |
| `FileText`            | `IconFileText` | Text file    |
| `File`                | `IconFile`     | File         |
| `Folder`              | `IconFolder`   | Folder       |
| `Download`            | `IconDownload` | Download     |
| `Upload`              | `IconUpload`   | Upload       |

### Status/Feedback

| Lucide          | Tabler              | Description   |
| --------------- | ------------------- | ------------- |
| `Check`         | `IconCheck`         | Confirm       |
| `X`             | `IconX`             | Close/Cancel  |
| `AlertCircle`   | `IconAlertCircle`   | Warning       |
| `AlertTriangle` | `IconAlertTriangle` | Danger        |
| `Info`          | `IconInfoCircle`    | Info          |
| `Loader2`       | `IconLoader2`       | Loading (spin)|
| `RefreshCw`     | `IconRefresh`       | Refresh       |
| `Eye`           | `IconEye`           | View          |
| `EyeOff`        | `IconEyeOff`        | Hide          |

### Devices/Network

| Lucide              | Tabler             | Description   |
| ------------------- | ------------------ | ------------- |
| `MonitorSmartphone` | `IconDevices`      | Devices       |
| `Smartphone`        | `IconDeviceMobile` | Mobile        |
| `Wifi`              | `IconWifi`         | WiFi          |
| `WifiOff`           | `IconWifiOff`      | WiFi off      |
| `Globe`             | `IconWorld`        | Global/Web    |

### Misc

| Lucide            | Tabler           | Description    |
| ----------------- | ---------------- | -------------- |
| `Type` (TextIcon) | `IconTypography` | Typography     |
| `Link` (LinkIcon) | `IconLink`       | Link           |
| `Calendar`        | `IconCalendar`   | Calendar       |
| `Clock`           | `IconClock`      | Clock          |
| `Mail`            | `IconMail`       | Mail           |
| `Phone`           | `IconPhone`      | Phone          |
| `MapPin`          | `IconMapPin`     | Location       |
| `Star`            | `IconStar`       | Star           |
| `Heart`           | `IconHeart`      | Heart          |
| `Bell`            | `IconBell`       | Notification   |
| `Filter`          | `IconFilter`     | Filter         |
| `Sort`            | `IconArrowsSort` | Sort           |

---

## Props Conversion

### strokeWidth → stroke

```tsx
// Lucide
<Search strokeWidth={2} />
<Search strokeWidth={1.5} />

// Tabler
<IconSearch stroke={2} />
<IconSearch stroke={1.5} />
```

### size

```tsx
// Lucide
<Search size={24} />
<Search size="1.5rem" />

// Tabler (same)
<IconSearch size={24} />
<IconSearch size="1.5rem" />
```

### className / style

```tsx
// Same usage
<IconSearch className="text-blue-500" />
<IconSearch style={{ color: 'blue' }} />
```

---

## Conversion Examples

### Import Conversion

```tsx
// Before
import {Monitor, Plus, Search, Settings, User} from 'lucide-react';

// After
import {IconDeviceDesktop, IconPlus, IconSearch, IconSettings, IconUser} from '@tabler/icons-react';
```

### Component Usage

```tsx
// Before
<Button>
  <Plus className="mr-2 h-4 w-4" />
  추가
</Button>

// After
<Button>
  <IconPlus className="mr-2 h-4 w-4" />
  추가
</Button>
```

---

## Bulk Conversion Regex

### Import Statement

```
// Find
from "lucide-react"

// Replace
from "@tabler/icons-react"
```

### Icon Names (common)

```
<Monitor → <IconDeviceDesktop
<Plus → <IconPlus
<Search → <IconSearch
<Pencil → <IconPencil
<Trash2 → <IconTrash
<Settings → <IconSettings
<ExternalLink → <IconExternalLink
<LogOut → <IconLogout
<User → <IconUser
<LayoutGrid → <IconLayoutGrid
<WifiOff → <IconWifiOff
<RefreshCw → <IconRefresh
<Image → <IconPhoto
<ImageIcon → <IconPhoto
<Video → <IconVideo
<Type → <IconTypography
<Link → <IconLink
<ArrowLeftRight → <IconArrowsLeftRight
<ChevronRight → <IconChevronRight
<LayoutDashboard → <IconLayoutDashboard
<FileText → <IconFileText
<Eye → <IconEye
<MonitorSmartphone → <IconDevices
<X → <IconX
<Check → <IconCheck
<Loader2 → <IconLoader2
```

---

## Search Commands

```bash
# Find lucide-react imports
Grep pattern="from ['\"]lucide-react['\"]" glob="*.{ts,tsx}" path="src/"

# Find specific icon usage
Grep pattern="<Monitor" glob="*.{ts,tsx}" path="src/"

# Find strokeWidth prop
Grep pattern="strokeWidth=" glob="*.{ts,tsx}" path="src/"
```

---

## Icon Search

For unknown icons, search on Tabler Icons site:

- https://tabler.io/icons

---

## Removal Targets

Remove after migration:

```bash
pnpm remove lucide-react
pnpm add @tabler/icons-react
```

---

## Notes

1. **Icon prefix**: All Tabler icons use `Icon` prefix
2. **strokeWidth → stroke**: Different prop name
3. **Some icon names differ**: Check Tabler Icons site
4. **Color**: Use `color` prop or CSS (same as Lucide)
