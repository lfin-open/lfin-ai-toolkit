---
name: admin-design-system
description: Route design system migration workflows to the right child skill. Use when you need an overview of the admin-design-system skill family or need help choosing between migrate-plan, migrate, and apply-composites.
---

# Admin Design System Skill Set

Use this meta-skill as the catalog/router for @l-fin/ui-components design system migration workflows.

## Use this skill when

- you need help choosing the right migration workflow
- you want an overview of the `admin-design-system/` skill family before invoking a child skill
- you want to understand the recommended migration sequence

## Child skills

### `migrate-plan`
- Path: `admin-design-system/migrate-plan/SKILL.md`
- Use when you have an existing project in `prev/` and need to generate a migration plan before executing.

### `migrate`
- Path: `admin-design-system/migrate/SKILL.md`
- Use when adding @l-fin/ui-components to an existing project or executing a plan from `migrate-plan`.

### `apply-composites`
- Path: `admin-design-system/apply-composites/SKILL.md`
- Use after running `migrate` to add advanced composite components (AppLayout, DataTable, FilterBuilder).

## Selection guide

| Goal | Use |
|------|-----|
| Generate a migration plan from `prev/` folder | `migrate-plan` |
| Execute migration to @l-fin/ui-components | `migrate` |
| Add AppLayout, DataTable, FilterBuilder after migration | `apply-composites` |

## Recommended sequence

1. `migrate-plan` — analyze the existing project and produce `MIGRATION_PLAN.md`
2. `migrate` — execute the plan and convert the project
3. `apply-composites` — layer in advanced composite components

## Reference

- Design system docs (llms.txt): https://admin-template.lpin.io/llms/llms.txt
  - Component catalog, migration guides, and pattern examples for @l-fin/ui-components (Mantine v8 based).
  - Detailed props and code examples are linked from sub-pages (e.g. `primitives/index.md`, `composites/index.md`).
