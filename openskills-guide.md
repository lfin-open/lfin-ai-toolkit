# openskills 사용 가이드

> **이 문서는 사용자(개발자)를 위한 가이드입니다.**
> Claude Code 외 다른 AI 에이전트(Codex, Cursor, Windsurf, Aider 등)에서 이 레포의 스킬을 사용하는 방법을 안내합니다.
> Claude Code 사용자는 [Git Skills 가이드](./git-skills-guide.md)를 참고하세요.

## openskills란?

이 레포의 스킬은 [Agent Skills 스펙](https://agentskills.io/specification)을 따릅니다.
[openskills](https://github.com/numman-ali/openskills)는 이 스펙을 따르는 스킬을 설치/관리하는 CLI 도구로, `AGENTS.md`에 스킬 목록을 등록하면 어떤 에이전트에서든 사용 가능합니다.

### 지원 에이전트

Claude Code, Codex, Cursor, Windsurf, Aider 등 `AGENTS.md`를 읽는 모든 에이전트

### 요구사항

- Node.js 20.6+
- Git

---

## Step 1: 스킬 설치

별도 글로벌 설치 없이 `npx`로 바로 사용합니다.

### 설치 옵션

설치 명령에 아래 옵션을 조합하여 스킬이 저장될 위치를 지정합니다.

| 옵션 | 설치 경로 | 적용 범위 | AGENTS.md 위치 |
|------|-----------|-----------|----------------|
| (기본) | `.claude/skills/` | 현재 프로젝트만 | `./AGENTS.md` |
| `--global` | `~/.claude/skills/` | 모든 프로젝트 | `~/AGENTS.md` |
| `--universal` | `.agent/skills/` | 현재 프로젝트 (에이전트 중립) | `./AGENTS.md` |
| `--global --universal` | `~/.agent/skills/` | 모든 프로젝트 (에이전트 중립) | `~/AGENTS.md` |

> 여러 에이전트를 혼용한다면 `--universal` 옵션을 사용하세요.
> `--global`을 사용하면 한 번만 설치해도 모든 프로젝트에서 사용 가능합니다.

### 카테고리별 설치

```bash
# git 스킬만 설치
npx openskills install l-fin/lfin-ai-toolkit/git

# admin-design-system 스킬만 설치
npx openskills install l-fin/lfin-ai-toolkit/admin-design-system
```

### 개별 스킬 설치

```bash
npx openskills install l-fin/lfin-ai-toolkit/git/commit
npx openskills install l-fin/lfin-ai-toolkit/git/create-pr
```

### 로컬에 클론한 경우

```bash
npx openskills install ./path/to/lfin-ai-toolkit            # 전체
npx openskills install ./path/to/lfin-ai-toolkit/git         # git만
npx openskills install ./path/to/lfin-ai-toolkit/git/commit  # commit만
```

---

## Step 2: AGENTS.md 동기화

설치 후 `sync`를 실행하면 `AGENTS.md`에 스킬 목록이 등록됩니다.

```bash
npx openskills sync
```

> `AGENTS.md`를 커밋하면 팀원 모두 같은 스킬 목록을 공유합니다.

---

## Step 3: 스킬 관리

```bash
npx openskills list                        # 설치된 스킬 목록 확인
npx openskills update                      # 모든 스킬 업데이트
npx openskills update commit,create-pr     # 특정 스킬만 업데이트
npx openskills manage                      # 스킬 삭제 (인터랙티브)
npx openskills remove commit               # 특정 스킬 삭제
```

---

## Step 4: 스킬 사용

설치 + sync 완료 후, 에이전트에서 스킬명으로 호출합니다.

```bash
/commit
/create-pr
/migrate
```

에이전트가 `AGENTS.md`의 스킬 목록을 읽고, description 기반으로 적절한 스킬을 자동 선택하거나 명시적 호출에 응답합니다.

---

## 스킬 목록

### git/

| 스킬 | 설명 |
|------|------|
| `commit` | 프로젝트 컨벤션에 맞는 커밋 메시지 생성 |
| `create-pr` | develop 브랜치로 PR 생성/업데이트 |
| `summarize-pr` | develop→main 머지 PR 본문 자동 요약 |

### admin-design-system/

| 스킬 | 설명 |
|------|------|
| `migrate` | 기존 프로젝트를 @l-fin/ui-components로 마이그레이션 |
| `migrate-plan` | prev/ 폴더 분석 후 마이그레이션 계획 파일 생성 |
| `apply-composites` | AppLayout, DataTable, FilterBuilder 적용 |

---

## 사전 요구사항 (Git remote 스킬)

`create-pr`, `summarize-pr` 스킬은 git remote 플랫폼에 따라 추가 설정이 필요합니다.

### GitHub 프로젝트

- `gh` CLI 설치 및 인증: `gh auth login`

### Gitea 프로젝트

환경변수 설정과 API 레퍼런스 배치가 필요합니다.

**1. 환경변수 설정**

```bash
# ~/.zshrc 또는 ~/.bashrc에 추가
export GITEA_URL="https://git.lfin.kr"
export GITEA_TOKEN="your-personal-access-token"
```

**2. gitea-api.md 배치**

openskills로 설치하면 `gitea-api.md`(80KB)는 포함되지 않습니다.
사용하는 에이전트 설정 경로에 수동으로 복사해주세요:

```bash
cp /path/to/lfin-ai-toolkit/git/references/gitea-api.md ~/.claude/gitea-api.md
cp /path/to/lfin-ai-toolkit/git/references/gitea-api.md ~/.codex/gitea-api.md
cp /path/to/lfin-ai-toolkit/git/references/gitea-api.md ~/.agent/gitea-api.md
```

상세 설정은 [Gitea 세팅 가이드](./gitea-setup-guide.md)를 참고하세요.
