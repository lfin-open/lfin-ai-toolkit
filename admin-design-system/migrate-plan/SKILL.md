---
name: migrate-plan
description: Analyze prev/ folder and generate MIGRATION_PLAN.md for /migrate skill. Use before running migrate when migrating an existing project placed in prev/.
---

# /migrate-plan - Migration Plan Generator

Analyze the existing project in `/prev` folder and generate a migration plan MD file for the `/migrate` skill.

## Output Language

**IMPORTANT**: 모든 출력은 한국어(Korean)로 작성합니다.

---

## Reference Documentation

Read @l-fin/ui-components docs for library mapping (use WebFetch):

| URL                                                                      | Description                              |
| ------------------------------------------------------------------------ | ---------------------------------------- |
| `https://admin-template.lpin.io/llms/llms.txt`                           | Component list, hooks, migration guide   |
| `https://admin-template.lpin.io/llms/getting-started/installation.md`    | Installation and CSS setup               |
| `https://admin-template.lpin.io/llms/theme-provider/theme-provider.md`   | ThemeProvider configuration              |
| `https://admin-template.lpin.io/llms/theme-provider/types.md`            | ThemeProviderProps, AppThemeConfig       |
| `https://admin-template.lpin.io/llms/reference/css-variables.md`         | Design tokens and CSS variables          |
| `https://admin-template.lpin.io/llms/primitives/types.md`                | Primitive component type definitions     |
| `https://admin-template.lpin.io/llms/primitives/dates-migration.md`      | @mantine/dates migration guide           |
| `https://admin-template.lpin.io/llms/composites/types.md`                | DataTable, FilterBuilder types           |
| `https://admin-template.lpin.io/llms/patterns/crud.md`                   | Complete CRUD page pattern               |

---

## Prerequisites

### P-1. Check prev folder exists

```bash
ls prev/
```

If prev folder missing, output:

```
❌ prev 폴더가 존재하지 않습니다.

마이그레이션할 기존 프로젝트를 prev/ 폴더에 복사해주세요.

예시:
cp -r /path/to/old-project ./prev
```

**IMPORTANT**: Stop skill execution if prev folder missing.

### P-2. Check prev/package.json exists

```bash
ls prev/package.json
```

If package.json missing, output:

```
❌ prev/package.json이 존재하지 않습니다.

유효한 Node.js 프로젝트를 prev/ 폴더에 복사해주세요.
```

**IMPORTANT**: Stop skill execution if package.json missing.

---

## Procedure

**Time tracking**: Measure time from skill start for final result.

### Step 1: Analyze Project Info

#### 1-1. Read package.json

```bash
Read prev/package.json
```

Extract:

- `name`: Project name
- `dependencies`: Runtime deps
- `devDependencies`: Dev deps

#### 1-2. Detect Framework

| Package             | Framework        | Additional Check                                   |
| ------------------- | ---------------- | -------------------------------------------------- |
| `next`              | Next.js          | `app/` exists → App Router, `pages/` → Pages Router|
| `react` (no next)   | React (Vite/CRA) | Check `vite.config.*` exists                       |
| `gatsby`            | Gatsby           | -                                                  |
| `remix`             | Remix            | -                                                  |

#### 1-3. Analyze Folder Structure

```bash
ls prev/app/ 2>/dev/null      # Next.js App Router
ls prev/pages/ 2>/dev/null    # Next.js Pages Router
ls prev/src/ 2>/dev/null      # src folder usage
ls prev/components/ 2>/dev/null
ls prev/components/ui/ 2>/dev/null  # shadcn/ui pattern
```

### Step 2: Detect UI Library

#### 2-1. Detect shadcn/ui

**Detection patterns**:

1. `prev/components/ui/` folder exists
2. Multiple `@radix-ui/*` packages used
3. `prev/components.json` exists

```bash
Glob pattern="prev/components/ui/*.tsx"
Read prev/components.json
```

**On shadcn/ui detection**:

- Reference: `references/ui/shadcn-to-lfin.md`
- List shadcn components (filenames in prev/components/ui/)

#### 2-2. Other UI Libraries

| Package            | Library    | Reference                            |
| ------------------ | ---------- | ------------------------------------ |
| `@mantine/core`    | Mantine    | `references/ui/mantine-to-lfin.md`   |
| `@mui/material`    | MUI        | `references/ui/mui-to-lfin.md`       |
| `@chakra-ui/react` | Chakra UI  | `references/ui/chakra-to-lfin.md`    |
| `antd`             | Ant Design | `references/ui/antd-to-lfin.md`      |
| `react-bootstrap`  | Bootstrap  | `references/ui/bootstrap-to-lfin.md` |

### Step 3: Detect Icon Library

| Package        | Library     | Reference                                   |
| -------------- | ----------- | ------------------------------------------- |
| `lucide-react` | Lucide      | `references/icons/lucide-to-tabler.md`      |
| `react-icons`  | React Icons | `references/icons/react-icons-to-tabler.md` |

**Search icon usage**:

```bash
Grep pattern="from ['\"]lucide-react['\"]" glob="*.{ts,tsx}" path="prev/"
Grep pattern="from ['\"]react-icons" glob="*.{ts,tsx}" path="prev/"
```

### Step 4: Detect Form Library

| Package               | Library         | Reference                                |
| --------------------- | --------------- | ---------------------------------------- |
| `react-hook-form`     | React Hook Form | `references/form/rhf-to-mantine-form.md` |
| `@hookform/resolvers` | RHF Resolvers   | (same as above)                          |
| `zod`                 | Zod             | Keep (compatible)                        |

**Search form usage**:

```bash
Grep pattern="from ['\"]react-hook-form['\"]" glob="*.{ts,tsx}" path="prev/"
Grep pattern="useForm" glob="*.{ts,tsx}" path="prev/"
```

### Step 5: Detect Routing (Next.js only)

For Next.js projects:

| Condition                 | Router       | Reference                                        |
| ------------------------- | ------------ | ------------------------------------------------ |
| `prev/app/` folder exists | App Router   | `references/routing/nextjs-to-tanstack.md`       |
| `prev/pages/` exists      | Pages Router | `references/routing/nextjs-pages-to-tanstack.md` |

**List route files**:

```bash
Glob pattern="prev/app/**/page.tsx"
Glob pattern="prev/pages/**/*.tsx"
```

### Step 6: Detect Other Libraries

| Package       | Library      | Migration Target                    | Reference                              |
| ------------- | ------------ | ----------------------------------- | -------------------------------------- |
| `next-themes` | Next Themes  | Replace with ThemeProvider          | `references/common/theme-provider.md`  |
| `sonner`      | Sonner Toast | Replace with @mantine/notifications | `references/common/toast-migration.md` |
| `date-fns`    | date-fns     | Keep (compatible)                   | -                                      |
| `recharts`    | Recharts     | Keep (compatible)                   | -                                      |
| `@dnd-kit/*`  | DnD Kit      | Keep (compatible)                   | -                                      |

### Step 7: Aggregate Migration Target Files

#### 7-1. UI Component Files

```bash
# shadcn/ui components (no migration needed - delete target)
Glob pattern="prev/components/ui/*.tsx"

# Custom components (need migration)
Glob pattern="prev/components/*.tsx"

# Components in app or pages
Glob pattern="prev/app/**/*.tsx"
Glob pattern="prev/pages/**/*.tsx"
```

#### 7-2. Analyze Library Usage Per File

Detect libraries used in each file:

```bash
Grep pattern="from ['\"]@/components/ui/" glob="*.tsx" path="prev/"
Grep pattern="from ['\"]lucide-react['\"]" glob="*.tsx" path="prev/"
Grep pattern="from ['\"]react-hook-form['\"]" glob="*.tsx" path="prev/"
```

### Step 8: Summary and User Confirmation

Show summary to user and get confirmation.

#### 8-1. Output Analysis Summary

```markdown
## 📋 prev 폴더 분석 결과

### 프로젝트 정보

| 항목           | 값                         |
| -------------- | -------------------------- |
| 프로젝트명     | {name}                     |
| 프레임워크     | {Next.js App Router / ...} |
| 분석 소요 시간 | {MM:SS}                    |

### 감지된 라이브러리

| 카테고리 | 현재            | 대상                 |
| -------- | --------------- | -------------------- |
| UI       | shadcn/ui       | @l-fin/ui-components |
| Icons    | lucide-react    | @tabler/icons-react  |
| Form     | react-hook-form | @mantine/form        |
| ...      | ...             | ...                  |

### 예상 작업량

- 라우트: {N}개
- 컴포넌트: {N}개
- 훅: {N}개
```

#### 8-2. User Confirmation

Use `AskUserQuestion` tool:

```
질문: "분석 결과를 바탕으로 마이그레이션 계획 파일을 생성할까요?"
옵션:
- "생성" - MIGRATION_PLAN.md 파일 생성
- "다시 분석" - prev 폴더 재분석
- "취소" - 스킬 종료
```

**IMPORTANT**: Do not generate plan file without user confirmation.

---

## Output: Generate Migration Plan File (Step 9)

After analysis, create `MIGRATION_PLAN.md` in project root.

### File Format

Output in Korean following this structure:

#### Header

```markdown
# 마이그레이션 계획

> 생성일: {YYYY-MM-DD HH:mm}
> 분석 대상: prev/
> 분석 소요 시간: {MM:SS}
```

#### Section 1: Project Overview

Table with project name, framework, router info.

#### Section 2: Detected Libraries

Two tables:
- Migration required: category, current lib, target lib, reference doc
- Keep (compatible): library name, version, note

#### Section 3: Folder Structure Mapping

Show current structure (prev/) and target structure (src/) as tree diagrams:

```
prev/
├── app/
│   ├── layout.tsx
│   └── page.tsx
├── components/
│   ├── ui/              # shadcn/ui (삭제 대상)
│   └── *.tsx
└── package.json
```

```
src/
├── routes/
│   ├── __root.tsx
│   └── index.tsx
├── components/
│   └── *.tsx            # 마이그레이션된 컴포넌트
└── main.tsx
```

#### Section 4: Migration Target Files

Three sub-sections:
- 4-1. Delete targets (shadcn/ui) - files replaced by @l-fin/ui-components
- 4-2. Route migration - Next.js routes → TanStack Router routes
- 4-3. Component migration - file path and changes needed

#### Section 5: Migration Order

Numbered list:
1. Routing setup - create TanStack Router route files
2. Layout migration - app/layout.tsx → routes/__root.tsx
3. Page migration - each page.tsx → routes/*.tsx
4. Component migration - UI/icons/form conversion
5. Cleanup - delete prev/components/ui/, remove unused packages

#### Section 6: Cautions

Checklist with:
- Delete `prev/components/ui/` folder after migration
- Remove `next-themes` after ThemeProvider replacement
- Remove `sonner` after @mantine/notifications replacement
- Replace Next.js features (`next/image`, `next/link`)

#### Section 7: Estimated Workload

Table with categories (routes, components, hooks, utils), file counts, complexity levels.

Footer note: `> 마이그레이션을 실행하려면 /lfin:migrate 를 사용하세요.`

---

## Arguments

- `--output <path>`: Output file path (default: `MIGRATION_PLAN.md`)
- `--prev <path>`: prev folder path (default: `prev/`)

## Examples

```
/migrate-plan                           # Default execution
/migrate-plan --output ./plans/v1.md    # Custom output path
/migrate-plan --prev ./old-project      # Custom prev path
```

## Notes

- Analysis result is compatible with `/migrate` skill's Step 2 (Detect Migration Targets) format
- Generated plan file can be manually reviewed and modified
- After generating plan file, run `/lfin:migrate` to execute migration
