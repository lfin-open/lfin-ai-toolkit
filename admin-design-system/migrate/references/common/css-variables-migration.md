# CSS Variables Migration

**IMPORTANT**: Use LFin design tokens instead of Mantine built-in CSS variables.

| Mantine Built-in (❌ Don't use) | LFin Token (✅ Recommended) |
| -------------------------------- | ------------------------- |
| `--mantine-color-default-border` | `--border-default`        |
| `--mantine-color-dimmed`         | `--text-dimmed`           |
| `--mantine-color-body`           | `--background-body`       |
| `--mantine-color-gray-light`     | `--background-gray-light` |
| `--mantine-primary-color-filled` | `--primary-filled`        |
| `--mantine-color-error-light`    | `--accent-light`          |
| `--mantine-color-error`          | `--accent-filled`         |
| `--mantine-color-success`        | `--success-filled`        |
| `--mantine-color-warning`        | `--warning-filled`        |

## Common LFin Design Tokens

| Usage                | Token                                         |
| -------------------- | --------------------------------------------- |
| Page background      | `--background-body`                           |
| Card/container bg    | `--background-default`                        |
| Default text         | `--text-default`                              |
| Secondary text       | `--text-dimmed`                               |
| Primary button/action| `--primary-filled`                            |
| Default border       | `--border-default`                            |
| Error/danger         | `--accent-filled`, `--accent-light`           |
| Success              | `--success-filled`, `--success-light`         |
| Warning              | `--warning-filled`, `--warning-light`         |
| Information          | `--information-filled`, `--information-light` |

## CSS Variable Application - Prefer style attribute

Use **style attribute** instead of Tailwind arbitrary value (`text-[var(...)]`).

```tsx
// ✅ 권장 - style 속성 사용
<span style={{color: 'var(--text-dimmed)'}}>보조 텍스트</span>
<div style={{borderColor: 'var(--border-default)', backgroundColor: 'var(--background-body)'}}>
  컨테이너
</div>

// ⚠️ 지양 - Tailwind arbitrary value
<span className="text-[var(--text-dimmed)]">...</span>  // 읽기 어려움
<span className="text-(--text-dimmed)">...</span>        // Tailwind 4 문법이지만 일관성 저하

// ❌ 금지 - Mantine 내장 변수 직접 사용
<div className="border-[var(--mantine-color-default-border)]" />
```

**Reasons**:
- Style attribute is more explicit and readable
- IDE CSS variable autocomplete support
- No need to learn Tailwind arbitrary value syntax
- Avoid Tailwind version syntax differences (`text-[var(...)]` vs `text-(...)`)

**Full token list**: See `https://admin-template.lpin.io/llms/reference/css-variables.md` (WebFetch)
