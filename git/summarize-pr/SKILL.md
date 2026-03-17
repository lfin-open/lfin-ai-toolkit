---
name: summarize-pr
description: Summarize develop→main merge PR and update PR body. Use when merging develop into main.
---

# PR Summary Generator

Summarize develop→main merge PR and update PR body.

> Before running this skill, read [detect-remote.md](references/detect-remote.md) to determine the git remote platform.

**Usage**: `/summarize-pr <PR_NUMBER>`

## Steps

### 1. Fetch PR info

- Parse owner/repo from git remote

**GitHub:**
- `gh pr view <PR_NUMBER> --json title,body,commits`

**Gitea:**
- `GET /repos/{owner}/{repo}/pulls/<PR_NUMBER>`
- `GET /repos/{owner}/{repo}/pulls/<PR_NUMBER>/commits`

### 2. Extract included feature PRs

- Extract `Merge pull request #XX` patterns from commit messages
- Fetch each feature PR for title, body, and changed files

**GitHub:**
- `gh pr view <NUMBER> --json title,body,files`

**Gitea:**
- `GET /repos/{owner}/{repo}/pulls/<NUMBER>`
- `GET /repos/{owner}/{repo}/pulls/<NUMBER>/files`

### 3. Generate summary

Compose using the template below:

```markdown
## Summary
- {feature summary} (#PR)
- `@l-fin/ui-components` X.X.X release (if applicable)

---

## Included Work

### {type}({scope}): {title} (#PR)

**Changes**:
- {change 1}
- {change 2}

**Behavior** (if applicable):
- {description}

---

## Changed Files

| File | Changes |
|------|---------|
| `{path}` | +{add} -{del} |
```

### 4. Update PR body

**GitHub:**
- `gh pr edit <PR_NUMBER> --body "..."`

**Gitea:**
- `PATCH /repos/{owner}/{repo}/pulls/<PR_NUMBER>` body field
- Refer to `references/gitea-api.md` for API spec

**On success:**
```
✅ PR #<PR_NUMBER> 본문이 업데이트되었습니다!
```

## Rules

- Output in **Korean**
- Reuse feature PR body content
- Include version info if release commit exists
- Exclude non-significant files (lock files, etc.) from Changed Files
