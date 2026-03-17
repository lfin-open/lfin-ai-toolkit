# React Icons → Tabler Icons

Migration guide from react-icons to @tabler/icons-react.

## Overview

| Item          | Value                         |
| ------------- | ----------------------------- |
| Difficulty    | Medium                        |
| Automatable   | 60~70%                        |
| Main work     | Import changes + name mapping |

react-icons bundles multiple icon sets (Font Awesome, Material, Feather, etc.), so mapping varies by icon set used.

---

## Core Changes

| react-icons                                   | @tabler/icons-react                                  |
| --------------------------------------------- | ---------------------------------------------------- |
| `import { FaUser } from 'react-icons/fa'`     | `import { IconUser } from '@tabler/icons-react'`     |
| `import { MdSettings } from 'react-icons/md'` | `import { IconSettings } from '@tabler/icons-react'` |
| `import { FiSearch } from 'react-icons/fi'`   | `import { IconSearch } from '@tabler/icons-react'`   |
| `size` prop                                   | `size` prop (same)                                   |
| `color` prop                                  | `color` prop (same)                                  |

---

## Icon Set Mapping

### Feather Icons (fi) → Tabler

Feather and Tabler have similar styles, making mapping relatively easy.

| react-icons/fi    | @tabler/icons-react | Description       |
| ----------------- | ------------------- | ----------------- |
| `FiUser`          | `IconUser`          | User              |
| `FiUsers`         | `IconUsers`         | Users             |
| `FiSettings`      | `IconSettings`      | Settings          |
| `FiSearch`        | `IconSearch`        | Search            |
| `FiPlus`          | `IconPlus`          | Add               |
| `FiMinus`         | `IconMinus`         | Remove            |
| `FiEdit`          | `IconEdit`          | Edit              |
| `FiTrash2`        | `IconTrash`         | Delete            |
| `FiX`             | `IconX`             | Close             |
| `FiCheck`         | `IconCheck`         | Confirm           |
| `FiHome`          | `IconHome`          | Home              |
| `FiMenu`          | `IconMenu2`         | Menu              |
| `FiChevronRight`  | `IconChevronRight`  | Right arrow       |
| `FiChevronLeft`   | `IconChevronLeft`   | Left arrow        |
| `FiChevronDown`   | `IconChevronDown`   | Down arrow        |
| `FiChevronUp`     | `IconChevronUp`     | Up arrow          |
| `FiArrowLeft`     | `IconArrowLeft`     | Left arrow        |
| `FiArrowRight`    | `IconArrowRight`    | Right arrow       |
| `FiExternalLink`  | `IconExternalLink`  | External link     |
| `FiDownload`      | `IconDownload`      | Download          |
| `FiUpload`        | `IconUpload`        | Upload            |
| `FiFile`          | `IconFile`          | File              |
| `FiFolder`        | `IconFolder`        | Folder            |
| `FiImage`         | `IconPhoto`         | Image             |
| `FiVideo`         | `IconVideo`         | Video             |
| `FiCalendar`      | `IconCalendar`      | Calendar          |
| `FiClock`         | `IconClock`         | Clock             |
| `FiMail`          | `IconMail`          | Mail              |
| `FiPhone`         | `IconPhone`         | Phone             |
| `FiMapPin`        | `IconMapPin`        | Location          |
| `FiStar`          | `IconStar`          | Star              |
| `FiHeart`         | `IconHeart`         | Heart             |
| `FiBell`          | `IconBell`          | Notification      |
| `FiFilter`        | `IconFilter`        | Filter            |
| `FiEye`           | `IconEye`           | View              |
| `FiEyeOff`        | `IconEyeOff`        | Hide              |
| `FiCopy`          | `IconCopy`          | Copy              |
| `FiRefreshCw`     | `IconRefresh`       | Refresh           |
| `FiLoader`        | `IconLoader2`       | Loading           |
| `FiAlertCircle`   | `IconAlertCircle`   | Warning           |
| `FiAlertTriangle` | `IconAlertTriangle` | Danger            |
| `FiInfo`          | `IconInfoCircle`    | Info              |
| `FiLogIn`         | `IconLogin`         | Login             |
| `FiLogOut`        | `IconLogout`        | Logout            |
| `FiMonitor`       | `IconDeviceDesktop` | Monitor           |
| `FiSmartphone`    | `IconDeviceMobile`  | Mobile            |
| `FiWifi`          | `IconWifi`          | WiFi              |
| `FiWifiOff`       | `IconWifiOff`       | WiFi off          |
| `FiGlobe`         | `IconWorld`         | Global            |
| `FiLink`          | `IconLink`          | Link              |

### Font Awesome (fa/fa6) → Tabler

| react-icons/fa           | @tabler/icons-react | Description       |
| ------------------------ | ------------------- | ----------------- |
| `FaUser`                 | `IconUser`          | User              |
| `FaUsers`                | `IconUsers`         | Users             |
| `FaCog` / `FaGear`       | `IconSettings`      | Settings          |
| `FaSearch`               | `IconSearch`        | Search            |
| `FaPlus`                 | `IconPlus`          | Add               |
| `FaMinus`                | `IconMinus`         | Remove            |
| `FaPencilAlt` / `FaEdit` | `IconEdit`          | Edit              |
| `FaTrash` / `FaTrashAlt` | `IconTrash`         | Delete            |
| `FaTimes`                | `IconX`             | Close             |
| `FaCheck`                | `IconCheck`         | Confirm           |
| `FaHome`                 | `IconHome`          | Home              |
| `FaBars`                 | `IconMenu2`         | Menu              |
| `FaChevronRight`         | `IconChevronRight`  | Right arrow       |
| `FaChevronLeft`          | `IconChevronLeft`   | Left arrow        |
| `FaChevronDown`          | `IconChevronDown`   | Down arrow        |
| `FaChevronUp`            | `IconChevronUp`     | Up arrow          |
| `FaArrowLeft`            | `IconArrowLeft`     | Left arrow        |
| `FaArrowRight`           | `IconArrowRight`    | Right arrow       |
| `FaExternalLinkAlt`      | `IconExternalLink`  | External link     |
| `FaDownload`             | `IconDownload`      | Download          |
| `FaUpload`               | `IconUpload`        | Upload            |
| `FaFile`                 | `IconFile`          | File              |
| `FaFolder`               | `IconFolder`        | Folder            |
| `FaImage`                | `IconPhoto`         | Image             |
| `FaVideo`                | `IconVideo`         | Video             |
| `FaCalendar`             | `IconCalendar`      | Calendar          |
| `FaClock`                | `IconClock`         | Clock             |
| `FaEnvelope`             | `IconMail`          | Mail              |
| `FaPhone`                | `IconPhone`         | Phone             |
| `FaMapMarkerAlt`         | `IconMapPin`        | Location          |
| `FaStar`                 | `IconStar`          | Star              |
| `FaHeart`                | `IconHeart`         | Heart             |
| `FaBell`                 | `IconBell`          | Notification      |
| `FaFilter`               | `IconFilter`        | Filter            |
| `FaEye`                  | `IconEye`           | View              |
| `FaEyeSlash`             | `IconEyeOff`        | Hide              |
| `FaCopy`                 | `IconCopy`          | Copy              |
| `FaSync` / `FaRedo`      | `IconRefresh`       | Refresh           |
| `FaSpinner`              | `IconLoader2`       | Loading           |
| `FaExclamationCircle`    | `IconAlertCircle`   | Warning           |
| `FaExclamationTriangle`  | `IconAlertTriangle` | Danger            |
| `FaInfoCircle`           | `IconInfoCircle`    | Info              |
| `FaSignInAlt`            | `IconLogin`         | Login             |
| `FaSignOutAlt`           | `IconLogout`        | Logout            |
| `FaDesktop`              | `IconDeviceDesktop` | Monitor           |
| `FaMobileAlt`            | `IconDeviceMobile`  | Mobile            |
| `FaWifi`                 | `IconWifi`          | WiFi              |
| `FaGlobe`                | `IconWorld`         | Global            |
| `FaLink`                 | `IconLink`          | Link              |

### Material Design (md) → Tabler

| react-icons/md         | @tabler/icons-react | Description       |
| ---------------------- | ------------------- | ----------------- |
| `MdPerson`             | `IconUser`          | User              |
| `MdPeople` / `MdGroup` | `IconUsers`         | Users             |
| `MdSettings`           | `IconSettings`      | Settings          |
| `MdSearch`             | `IconSearch`        | Search            |
| `MdAdd`                | `IconPlus`          | Add               |
| `MdRemove`             | `IconMinus`         | Remove            |
| `MdEdit`               | `IconEdit`          | Edit              |
| `MdDelete`             | `IconTrash`         | Delete            |
| `MdClose`              | `IconX`             | Close             |
| `MdCheck`              | `IconCheck`         | Confirm           |
| `MdHome`               | `IconHome`          | Home              |
| `MdMenu`               | `IconMenu2`         | Menu              |
| `MdChevronRight`       | `IconChevronRight`  | Right arrow       |
| `MdChevronLeft`        | `IconChevronLeft`   | Left arrow        |
| `MdExpandMore`         | `IconChevronDown`   | Down arrow        |
| `MdExpandLess`         | `IconChevronUp`     | Up arrow          |
| `MdArrowBack`          | `IconArrowLeft`     | Left arrow        |
| `MdArrowForward`       | `IconArrowRight`    | Right arrow       |
| `MdOpenInNew`          | `IconExternalLink`  | External link     |
| `MdFileDownload`       | `IconDownload`      | Download          |
| `MdFileUpload`         | `IconUpload`        | Upload            |
| `MdInsertDriveFile`    | `IconFile`          | File              |
| `MdFolder`             | `IconFolder`        | Folder            |
| `MdImage`              | `IconPhoto`         | Image             |
| `MdVideocam`           | `IconVideo`         | Video             |
| `MdCalendarToday`      | `IconCalendar`      | Calendar          |
| `MdAccessTime`         | `IconClock`         | Clock             |
| `MdEmail` / `MdMail`   | `IconMail`          | Mail              |
| `MdPhone`              | `IconPhone`         | Phone             |
| `MdLocationOn`         | `IconMapPin`        | Location          |
| `MdStar`               | `IconStar`          | Star              |
| `MdFavorite`           | `IconHeart`         | Heart             |
| `MdNotifications`      | `IconBell`          | Notification      |
| `MdFilterList`         | `IconFilter`        | Filter            |
| `MdVisibility`         | `IconEye`           | View              |
| `MdVisibilityOff`      | `IconEyeOff`        | Hide              |
| `MdContentCopy`        | `IconCopy`          | Copy              |
| `MdRefresh`            | `IconRefresh`       | Refresh           |
| `MdError`              | `IconAlertCircle`   | Warning           |
| `MdWarning`            | `IconAlertTriangle` | Danger            |
| `MdInfo`               | `IconInfoCircle`    | Info              |
| `MdLogin`              | `IconLogin`         | Login             |
| `MdLogout`             | `IconLogout`        | Logout            |
| `MdComputer`           | `IconDeviceDesktop` | Monitor           |
| `MdSmartphone`         | `IconDeviceMobile`  | Mobile            |
| `MdWifi`               | `IconWifi`          | WiFi              |
| `MdWifiOff`            | `IconWifiOff`       | WiFi off          |
| `MdLanguage`           | `IconWorld`         | Global            |
| `MdLink`               | `IconLink`          | Link              |

---

## Props Conversion

### size

```tsx
// react-icons
<FiUser size={24} />
<FiUser size="1.5rem" />

// @tabler/icons-react (same)
<IconUser size={24} />
<IconUser size="1.5rem" />
```

### color

```tsx
// react-icons
<FiUser color="blue" />
<FiUser color="#0066ff" />

// @tabler/icons-react (same)
<IconUser color="blue" />
<IconUser color="#0066ff" />
```

### className / style

```tsx
// Same usage
<IconUser className="text-blue-500" />
<IconUser style={{ color: 'blue' }} />
```

### stroke (Tabler only)

```tsx
// react-icons - strokeWidth not supported (varies by icon set)

// @tabler/icons-react - adjustable stroke width
<IconUser stroke={2} />     // default
<IconUser stroke={1.5} />   // thinner
```

---

## Conversion Examples

### Import Conversion

```tsx
// Before (mixed sets)
import {FiUser, FiSettings} from 'react-icons/fi';
import {FaHome, FaSearch} from 'react-icons/fa';
import {MdEmail, MdPhone} from 'react-icons/md';

// After (unified)
import {
  IconUser,
  IconSettings,
  IconHome,
  IconSearch,
  IconMail,
  IconPhone,
} from '@tabler/icons-react';
```

### Component Usage

```tsx
// Before
<Button>
  <FiPlus size={16} className="mr-2" />
  추가
</Button>

// After
<Button>
  <IconPlus size={16} className="mr-2" />
  추가
</Button>
```

---

## Bulk Conversion Regex

### Remove Import Statements

```bash
# Find and replace with @tabler/icons-react import
from ['"]react-icons/fi['"]
from ['"]react-icons/fa['"]
from ['"]react-icons/fa6['"]
from ['"]react-icons/md['"]
```

### Icon Name Conversion (common)

Feather (Fi*):
```
FiUser → IconUser
FiSettings → IconSettings
FiSearch → IconSearch
FiPlus → IconPlus
FiTrash2 → IconTrash
FiEdit → IconEdit
FiX → IconX
FiCheck → IconCheck
FiHome → IconHome
FiMenu → IconMenu2
```

Font Awesome (Fa*):
```
FaUser → IconUser
FaCog → IconSettings
FaSearch → IconSearch
FaPlus → IconPlus
FaTrash → IconTrash
FaTimes → IconX
FaCheck → IconCheck
FaHome → IconHome
FaBars → IconMenu2
```

Material (Md*):
```
MdPerson → IconUser
MdSettings → IconSettings
MdSearch → IconSearch
MdAdd → IconPlus
MdDelete → IconTrash
MdClose → IconX
MdCheck → IconCheck
MdHome → IconHome
MdMenu → IconMenu2
```

---

## Search Commands

```bash
# Find react-icons imports (all sets)
Grep pattern="from ['\"]react-icons/" glob="*.{ts,tsx}" path="src/"

# Find specific set
Grep pattern="from ['\"]react-icons/fi['\"]" glob="*.{ts,tsx}" path="src/"
Grep pattern="from ['\"]react-icons/fa['\"]" glob="*.{ts,tsx}" path="src/"
Grep pattern="from ['\"]react-icons/md['\"]" glob="*.{ts,tsx}" path="src/"

# Find icon usage
Grep pattern="<Fi[A-Z]" glob="*.{ts,tsx}" path="src/"
Grep pattern="<Fa[A-Z]" glob="*.{ts,tsx}" path="src/"
Grep pattern="<Md[A-Z]" glob="*.{ts,tsx}" path="src/"
```

---

## Icon Search

For unknown icons, search on Tabler Icons site:

- https://tabler.io/icons

---

## Removal Targets

Remove after migration:

```bash
pnpm remove react-icons
pnpm add @tabler/icons-react
```

---

## Notes

1. **Icon prefix**: All Tabler icons use `Icon` prefix
2. **Unified sets**: Multiple react-icons sets consolidated into one
3. **Style differences**: Font Awesome (filled) vs Feather (line) have different feels
   - Tabler is similar to Feather (line style)
   - For filled versions, use `IconUserFilled` etc.
4. **Some icons missing**: Special react-icons may not exist in Tabler
   - Search for alternatives on Tabler Icons site
5. **stroke prop**: Tabler-only feature for adjustable line thickness
