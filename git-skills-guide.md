# Git Skills 사용 가이드 (Claude Code)

> **이 문서는 사용자(개발자)를 위한 가이드입니다.**
> Claude Code에서 Git 관련 AI 스킬을 설치하고 사용하는 방법을 안내합니다.
> 다른 AI 에이전트(Codex, Cursor 등)를 사용한다면 [openskills 가이드](./openskills-guide.md)를 참고하세요.

## 제공 스킬

| 스킬 | 설명 | remote 사용 |
|------|------|:-----------:|
| `git` | Git 스킬 세트를 안내하는 메타/카탈로그 스킬 | - |
| `commit` | 프로젝트 컨벤션에 맞는 커밋 메시지 생성 | - |
| `create-pr` | develop 브랜치로 PR 생성/업데이트 | O |
| `summarize-pr` | develop→main 머지 PR 본문 자동 요약 | O |

> remote 사용 스킬은 GitHub(`gh`) 또는 Gitea(REST API)를 자동 감지하여 동작합니다.

---

## 사전 요구사항

### GitHub 프로젝트

- `gh` CLI 설치 및 인증만 되어 있으면 바로 사용 가능
  ```bash
  gh auth login
  ```

### Gitea 프로젝트

- 환경변수 및 API 레퍼런스 설정이 필요합니다
- [Gitea 세팅 가이드](./gitea-setup-guide.md)를 따라 설정하세요

---

## 설치 방법

```bash
# 전체 복사 (권장 — meta 스킬 + child 스킬 + shared source 유지)
cp -r /path/to/lfin-ai-toolkit/git .claude/skills/git

# 또는 개별 스킬만 복사
cp -r /path/to/lfin-ai-toolkit/git/commit .claude/skills/commit
```

> `commit`만 사용한다면 `commit/` 폴더 하나만 복사하면 됩니다.
> `create-pr`, `summarize-pr`은 각 스킬 내에 `references/detect-remote.md`와
> `references/gitea-api.md`가 포함되어 있어 개별 복사도 동작합니다.

---

## 사용법

```bash
/commit              # 커밋 생성
/create-pr           # PR 생성 (현재 브랜치 → develop)
/summarize-pr 42     # PR 본문 요약 업데이트
```

## 폴더 구조

```
git/
  SKILL.md                 # Git 스킬 세트를 안내하는 메타/카탈로그 스킬
  references/              # 레포 내 공유 레퍼런스 (원본)
    detect-remote.md       # GitHub vs Gitea 자동 감지 로직
    gitea-api.md           # Gitea REST API 스펙 (467 endpoints)
  commit/
    SKILL.md
  create-pr/
    SKILL.md
    references/
      detect-remote.md     # 스킬 내 포함 (자체 완결)
      gitea-api.md         # 스킬 내 포함 (runtime mirror)
  summarize-pr/
    SKILL.md
    references/
      detect-remote.md     # 스킬 내 포함 (자체 완결)
      gitea-api.md         # 스킬 내 포함 (runtime mirror)
```

## 레퍼런스 동작 방식

- `git/SKILL.md`는 `git/` 세트를 설명하고 child skill로 라우팅하는 메타 스킬입니다
- `commit`은 remote를 안 쓰므로 레퍼런스 참조 없음
- `create-pr`, `summarize-pr`는 각 스킬 내 `references/detect-remote.md`를 참조
- Gitea로 감지되면 각 스킬 내 `references/gitea-api.md`를 우선 사용합니다
- 필요하면 글로벌 경로(`~/.claude/gitea-api.md` 등)를 fallback으로 사용할 수 있습니다
- GitHub이면 `gitea-api.md`를 로딩하지 않음

## 커밋 메시지 컨벤션

`commit` 스킬은 브랜치명에서 Jira 티켓을 자동 추출합니다.

```
LFDS-4: feat(Radio): Radio 컴포넌트 래퍼 구현
```

| 브랜치 | 추출 티켓 |
|--------|-----------|
| `feature/LFDS-4_Radio-재정의` | `LFDS-4` |
| `fix/LFDS-12_button-style` | `LFDS-12` |
| `develop`, `main` | 티켓 생략 |
