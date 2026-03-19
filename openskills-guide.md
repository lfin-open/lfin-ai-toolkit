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

### 개별 스킬 설치

스킬 단위로 설치합니다. subpath는 `SKILL.md`가 있는 디렉토리까지 지정해야 합니다.
`git`는 카탈로그 성격의 메타 스킬이므로, 실제 Git 작업을 하려면 child skill(`commit`, `create-pr`, `summarize-pr`) 설치를 기준으로 생각하는 것이 안전합니다.

```bash
# git 스킬
npx openskills install lfin-open/lfin-ai-toolkit/git/commit
npx openskills install lfin-open/lfin-ai-toolkit/git/create-pr
npx openskills install lfin-open/lfin-ai-toolkit/git/summarize-pr

# admin-design-system 스킬
npx openskills install lfin-open/lfin-ai-toolkit/admin-design-system/migrate
npx openskills install lfin-open/lfin-ai-toolkit/admin-design-system/migrate-plan
npx openskills install lfin-open/lfin-ai-toolkit/admin-design-system/apply-composites
```

### 로컬에 클론한 경우

```bash
npx openskills install ./path/to/lfin-ai-toolkit/git/commit
npx openskills install ./path/to/lfin-ai-toolkit/git/create-pr
```

---

## Step 2: AGENTS.md 동기화

설치 후 `sync`를 실행하면 `AGENTS.md`에 스킬 목록이 등록됩니다.
`sync`는 기본적으로 **현재 디렉토리**의 `./AGENTS.md`에 씁니다.

```bash
# 프로젝트 내 AGENTS.md에 등록 (프로젝트 디렉토리에서 실행)
npx openskills sync

# 글로벌 AGENTS.md에 등록 (--global로 설치한 경우)
npx openskills sync -o ~/AGENTS.md
```

> `--global`로 스킬을 설치했다면 `sync`도 `-o ~/AGENTS.md`로 실행해야 합니다.
> 프로젝트 내 `AGENTS.md`를 커밋하면 팀원 모두 같은 스킬 목록을 공유합니다.

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

### 동작 원리

`npx openskills sync`를 실행하면 `AGENTS.md`에 아래와 같은 XML 블록이 생성됩니다.

```xml
<skills_system priority="1">
<usage>
How to use skills:
- Invoke: `npx openskills read <skill-name>` (run in your shell)
  - For multiple: `npx openskills read skill-one,skill-two`
- The skill content will load with detailed instructions ...
</usage>
<available_skills>
  <skill>
    <name>commit</name>
    <description>Create git commit following project conventions. ...</description>
  </skill>
  ...
</available_skills>
</skills_system>
```

에이전트는 세션 시작 시 `AGENTS.md`를 읽고, 사용자의 요청이 스킬의 `description`과 매칭되면 자동으로 `npx openskills read <skill-name>`을 shell에서 실행합니다. 이 명령은 해당 `SKILL.md`의 전체 내용을 stdout으로 출력하고, 에이전트가 그 지시사항을 따릅니다.

### 에이전트별 차이

| 에이전트 | 스킬 로딩 방식 |
|----------|---------------|
| **Claude Code** | `.claude/skills/`를 직접 읽음. `/commit` 같은 슬래시 명령 지원 |
| **Cursor, Windsurf, Codex, Aider 등** | `AGENTS.md`의 `<available_skills>` 카탈로그를 읽고, 매칭 시 `npx openskills read`를 shell로 실행 |

Claude Code 외 에이전트에서는 자연어로 요청합니다.

```
커밋해줘
PR 만들어줘
마이그레이션 진행해줘
```

에이전트가 자동으로 트리거하지 않는 경우, 직접 실행을 지시할 수 있습니다.

```
npx openskills read commit 실행해서 그 지시사항대로 커밋해줘
```

### 스킬이 트리거되지 않을 때

`description` 필드가 에이전트의 유일한 판단 기준입니다. 자동 트리거가 안 되면:

1. `npx openskills read <skill-name>` 실행을 직접 지시
2. 또는 `AGENTS.md`의 스킬 목록을 참고하라고 안내

---

## 스킬 목록

### git/

| 스킬 | 설명 |
|------|------|
| `git` | Git 스킬 세트를 안내하는 메타/카탈로그 스킬 |
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

## 추가 설정

### Git remote 스킬 (`create-pr`, `summarize-pr`)

GitHub 프로젝트는 `gh` CLI 인증만 되어 있으면 바로 사용 가능합니다.
Gitea 프로젝트는 환경변수와 API 레퍼런스 파일 설정이 필요합니다. [Gitea 세팅 가이드](./gitea-setup-guide.md)를 참고하세요.

### openskills 설치 시 Gitea 레퍼런스 동작

- 이 레포의 최신 `create-pr`, `summarize-pr` 디렉토리에는 각 스킬별
  `references/gitea-api.md` runtime mirror가 포함되어 있습니다.
- 따라서 해당 child skill을 직접 설치하면 Gitea 프로젝트에서도 로컬 reference로 동작할 수 있습니다.
- 글로벌 `~/.claude/gitea-api.md`, `~/.codex/gitea-api.md`, `~/.agent/gitea-api.md` 배치는
  fallback 또는 다른 Gitea 작업 프롬프트를 위한 선택 사항입니다.
- 자세한 운영 방식은 [Gitea 세팅 가이드](./gitea-setup-guide.md)를 참고하세요.
