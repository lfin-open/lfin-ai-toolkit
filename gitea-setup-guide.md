# Gitea REST API 로컬 세팅 가이드

> **이 문서는 사용자(개발자)를 위한 가이드입니다.**
> AI 도구(Claude Code, Codex)에서 Gitea를 REST API로 연동하기 위한 로컬 환경 설정 방법을 안내합니다.

## tea CLI 대신 REST API를 쓰는 이유

| | tea CLI | Gitea REST API |
|---|---------|----------------|
| 설치 | PC마다 `tea` 바이너리 설치 필요 | 불필요 (`curl`만 있으면 됨) |
| 인증 | `tea login add`로 사전 설정 | 환경변수 `GITEA_TOKEN`만 설정 |
| 기능 범위 | PR, Issue 등 주요 기능만 | 467개 전체 엔드포인트 |
| AI 도구 호환 | 설치 여부에 따라 동작이 달라짐 | 어디서든 동일하게 동작 |
| 버전 관리 | `tea` 버전 ≠ Gitea 서버 버전 | 서버 API 스펙에만 의존 |

## Step 1: 환경변수 설정

`~/.zshrc` (또는 `~/.bashrc`)에 추가:

```bash
export GITEA_URL="https://git.lfin.kr"
export GITEA_TOKEN="your-personal-access-token"
```

설정 후 반영:

```bash
source ~/.zshrc
```

**토큰 발급**: Gitea 웹 → Settings → Applications → Generate New Token

## Step 2: API 레퍼런스 파일 복사

이 레포의 `git/references/gitea-api.md` 파일을 AI 도구 설정 디렉토리에 복사합니다.

**Claude Code 사용 시:**

```bash
cp /path/to/lfin-ai-toolkit/git/references/gitea-api.md ~/.claude/gitea-api.md
```

**Codex 사용 시:**

```bash
cp /path/to/lfin-ai-toolkit/git/references/gitea-api.md ~/.codex/gitea-api.md
```

> 이 파일은 467개 엔드포인트 + 216개 스키마를 한 줄씩 요약한 인덱스입니다 (748줄).
> AI가 Gitea 작업이 필요할 때만 읽으며, 매 세션마다 로드되지 않습니다.

## Step 3: CLAUDE.md / AGENTS.md에 Gitea 섹션 추가

### Claude Code (`~/.claude/CLAUDE.md` 또는 프로젝트 `CLAUDE.md`)

```markdown
## Gitea Integration

- Gitea 작업 시 REST API(curl) 사용
- Base URL: ${GITEA_URL}/api/v1
- 인증: -H "Authorization: token ${GITEA_TOKEN}"
- owner/repo: git remote get-url origin에서 자동 파싱
- API 레퍼런스: ~/.claude/gitea-api.md 파일을 읽고 엔드포인트와 스키마 확인
```

### Codex (`~/.codex/AGENTS.md` 또는 프로젝트 `AGENTS.md`)

```markdown
## Gitea Integration

- Gitea 작업 시 REST API(curl) 사용
- Base URL: ${GITEA_URL}/api/v1
- 인증: -H "Authorization: token ${GITEA_TOKEN}"
- owner/repo: git remote get-url origin에서 자동 파싱
- API 레퍼런스: ~/.codex/gitea-api.md 파일을 읽고 엔드포인트와 스키마 확인
```

> **주의**: `@gitea-api.md`처럼 직접 참조하면 매 세션마다 전체 파일(77.6K)이 로드됩니다.
> 위처럼 파일 경로만 안내하면 AI가 필요할 때만 파일을 읽어 토큰을 절약합니다.

## Step 4: 설정 확인

```bash
# 환경변수 확인
echo $GITEA_URL
echo $GITEA_TOKEN

# API 접근 테스트
curl -s -H "Authorization: token $GITEA_TOKEN" "$GITEA_URL/api/v1/user" | jq .login
```

본인의 Gitea 사용자명이 출력되면 설정 완료입니다.

## API 레퍼런스 활용 팁

- `gitea-api.md`에서 `operationId`나 스키마명으로 원하는 엔드포인트 검색
- 상세 필드 정보는 Swagger UI에서 확인: `${GITEA_URL}/api/swagger`
- Gitea 서버 업그레이드 시 이 파일만 새로 생성하면 됨

## 체크리스트

- [ ] `GITEA_URL`, `GITEA_TOKEN` 환경변수 설정
- [ ] `gitea-api.md` 파일을 `~/.claude/` 또는 `~/.codex/`에 복사
- [ ] `CLAUDE.md` 또는 `AGENTS.md`에 Gitea Integration 섹션 추가
