# AGENTS

This repo follows the [Agent Skills specification](https://agentskills.io/specification).

## Agent Skills Spec Rules

When creating or modifying skills in this repo, follow these rules:

- Each skill = one directory + `SKILL.md`
- `name` field: required, 1-64 chars, lowercase alphanumeric + hyphens only, must match directory name
- `description` field: required, 1-1024 chars, must describe what it does AND when to use it
- Allowed frontmatter fields: `name`, `description`, `license`, `compatibility`, `metadata`, `allowed-tools`
- No custom frontmatter fields
- `SKILL.md` should be under 500 lines — extract detailed content to `references/`
- Each skill should be self-contained with its own `references/` when needed
- Skill instructions in English, user-facing output in Korean

## Available Skills

### git/

| Skill | Description |
|-------|-------------|
| [`commit`](./git/commit/SKILL.md) | Create git commit following project conventions |
| [`create-pr`](./git/create-pr/SKILL.md) | Prepare and create PR to develop branch |
| [`summarize-pr`](./git/summarize-pr/SKILL.md) | Summarize develop→main merge PR and update PR body |

### admin-design-system/

| Skill | Description |
|-------|-------------|
| [`migrate`](./admin-design-system/migrate/SKILL.md) | Migrate existing project to @l-fin/ui-components design system |
| [`migrate-plan`](./admin-design-system/migrate-plan/SKILL.md) | Analyze prev/ folder and generate MIGRATION_PLAN.md |
| [`apply-composites`](./admin-design-system/apply-composites/SKILL.md) | Apply Composite components (AppLayout, DataTable, FilterBuilder) |
