# Git Remote Detection

Detect whether the current project uses GitHub or Gitea, and select the appropriate tool.

## Detection

```bash
REMOTE_URL=$(git remote get-url origin)
```

## Criteria

| Pattern | Platform | Tool |
|---|---|---|
| Contains `github.com` | GitHub | `gh` CLI |
| Otherwise | Gitea | Gitea REST API (`curl`) |

## GitHub (`gh` CLI)

- Assumes `gh` CLI is installed and authenticated.
- e.g. `gh pr create`, `gh pr list`, `gh issue create`

## Gitea (REST API)

### Environment Variables

When detected as Gitea, verify the following environment variables are set.

| Variable | Purpose |
|---|---|
| `GITEA_URL` | Gitea instance base URL (e.g. `https://git.lfin.kr`) |
| `GITEA_TOKEN` | Gitea API auth token |

If either is missing, print the message below and **STOP**:

```
❌ Gitea 환경변수가 설정되지 않았습니다.

~/.zshrc에 아래 내용을 추가해주세요:
  export GITEA_URL="https://your-gitea-instance.com"
  export GITEA_TOKEN="your-token"

설정 후 셸을 재시작하거나 `source ~/.zshrc`를 실행해주세요.
```

### API Access

- Base URL: `${GITEA_URL}/api/v1`
- Auth: `-H "Authorization: token $GITEA_TOKEN"` header
- owner/repo: parse from remote URL

```bash
# Extract Gitea API info from remote URL
REMOTE_URL=$(git remote get-url origin)
GITEA_HOST=$(echo "$REMOTE_URL" | sed -E 's#^https?://([^/]+)/.*#\1#; s#^git@([^:]+):.*#\1#')
GITEA_BASE="https://${GITEA_HOST}/api/v1"
OWNER_REPO=$(echo "$REMOTE_URL" | sed -E 's#^https?://[^/]+/##; s#^git@[^:]+:##; s#\.git$##')
OWNER=$(echo "$OWNER_REPO" | cut -d'/' -f1)
REPO=$(echo "$OWNER_REPO" | cut -d'/' -f2)
```

- When detected as Gitea, additionally read `gitea-api.md` for API spec. Search in this order:
  1. `references/gitea-api.md` (same folder as this file)
  2. `~/.claude/gitea-api.md` (global config)
  3. `~/.codex/gitea-api.md` (global config for Codex)
  4. `~/.agent/gitea-api.md` (universal agent config)
- When detected as GitHub, do NOT read `gitea-api.md`.
