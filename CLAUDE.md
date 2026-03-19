# lfin-ai-toolkit

LFIN 사내 공용 AI 스킬 모음 레포.

## Agent Skills Spec Compliance

All skills in this repo MUST follow the [Agent Skills specification](https://agentskills.io/specification).

### Required

- Each skill = one directory + `SKILL.md`
- `name`: required, 1-64 chars, lowercase alphanumeric + hyphens only, MUST match directory name
- `description`: required, 1-1024 chars, must describe **what it does AND when to use it**
- Frontmatter fields allowed: `name`, `description`, `license`, `compatibility`, `metadata`, `allowed-tools`
- No custom frontmatter fields

### Recommended

- `SKILL.md` under 500 lines — extract detailed content to `references/`
- `references/` for supplemental docs (loaded on demand)
- `scripts/` for executable code
- `assets/` for templates and data files
- Keep reference files focused and small for efficient context usage

### Conventions (repo-specific)

- Skill instructions in **English** (token efficiency)
- User-facing output messages in **Korean**
- Code and comments as-is
- Each skill should be self-contained (include necessary `references/` within the skill directory)
- Each child skill maintains its own `references/` directory for self-contained operation

## Repository Structure

### Git & Git Remote Skills

- `./git/SKILL.md` — Meta-skill / catalog router for the git skill family
- `./git/commit/SKILL.md` — Commit generator
- `./git/create-pr/SKILL.md` — PR creator (references in `./git/create-pr/references/`)
- `./git/summarize-pr/SKILL.md` — PR summarizer (references in `./git/summarize-pr/references/`)

### Design System Migration Skills

- `./admin-design-system/SKILL.md` — Meta-skill / catalog router for the admin-design-system skill family
- `./admin-design-system/migrate/SKILL.md` — Migration executor (references in `./admin-design-system/migrate/references/`)
- `./admin-design-system/migrate-plan/SKILL.md` — Migration planner
- `./admin-design-system/apply-composites/SKILL.md` — Composite components (references in `./admin-design-system/apply-composites/references/`)

## User-Facing Guides (root)

- [git-skills-guide.md](./git-skills-guide.md) — Claude Code 기준 Git 스킬 설치/사용 가이드
- [design-system-skills-guide.md](./design-system-skills-guide.md) — Claude Code 기준 디자인 시스템 마이그레이션 스킬 가이드
- [openskills-guide.md](./openskills-guide.md) — 다른 에이전트용 openskills 설치/사용 가이드
- [gitea-setup-guide.md](./gitea-setup-guide.md) — Gitea REST API 로컬 세팅 가이드
- [contributing-guide.md](./contributing-guide.md) — 스킬 추가/수정 절차 및 브랜치 전략 가이드
