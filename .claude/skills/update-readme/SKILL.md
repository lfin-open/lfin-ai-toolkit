---
name: update-readme
description: Scan repo structure and regenerate README.md with updated skill list and guide links. Use after adding, removing, or renaming skills or guide documents.
---

# Update README.md

Scan the repo structure and regenerate `README.md` with the latest skill list and guide document links.

## When to Run

- After adding a new skill
- After removing or renaming a skill
- After adding or renaming a guide document
- After changing a skill's `description` in SKILL.md frontmatter

## Procedure

### 1. Scan guide documents

Find all `*-guide.md` files in the repo root:

```bash
Glob pattern="*-guide.md"
```

For each guide, read the first 3 lines to extract the title and description.

### 2. Scan all skills

Find all `SKILL.md` files recursively:

```bash
Glob pattern="**/SKILL.md"
```

Exclude `.claude/`, `.agent/`, `node_modules/` paths.

For each SKILL.md, extract `name` and `description` from YAML frontmatter.
Group skills by their parent category directory (e.g., `git/`, `admin-design-system/`).

### 3. Scan AGENTS.md and CLAUDE.md

Check if `AGENTS.md` and `CLAUDE.md` exist in root. These will be linked in README if present.

### 4. Generate README.md

Write `README.md` in the repo root following this template:

```markdown
# lfin-ai-toolkit

LFIN 사내 공용 AI 스킬 모음 레포. [Agent Skills 스펙](https://agentskills.io/specification)을 따릅니다.

## 가이드 문서

| 문서 | 대상 | 설명 |
|------|------|------|
{for each guide document, one row}

## 스킬 목록

### [{category}/](./{category}/)

| 스킬 | 설명 |
|------|------|
{for each skill in category, one row with link to SKILL.md}
```

**Rules for guide table:**
- "대상" column: infer from document content (Claude Code 사용자 / 그 외 에이전트 사용자 / 전체)
- "설명" column: extract from the document's subtitle or first blockquote

**Rules for skill table:**
- Link skill name to `./category/skill-name/SKILL.md`
- Use `description` from frontmatter, translated to Korean if currently in English
- Sort skills alphabetically within each category
- Sort categories alphabetically

### 5. Update CLAUDE.md repository structure

If `CLAUDE.md` exists, update the "Repository Structure" section to reflect current skill paths.

### 6. Update AGENTS.md skill list

If `AGENTS.md` exists, update the "Available Skills" section to reflect current skills with links.

### 7. Show summary

```
✅ README.md 업데이트 완료

변경사항:
- 가이드 문서: {N}개
- 스킬 카테고리: {N}개
- 총 스킬: {N}개
- CLAUDE.md 구조 업데이트: ✅/⏭️
- AGENTS.md 스킬 목록 업데이트: ✅/⏭️
```

## Rules

- Output messages in **Korean**
- Do NOT modify any SKILL.md files — read only
- Do NOT modify guide documents — read only
- Only write to `README.md`, `CLAUDE.md` (structure section), `AGENTS.md` (skill list section)
- Preserve any manually added sections in README.md that are not auto-generated
