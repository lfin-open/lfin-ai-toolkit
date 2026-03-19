# 기여 가이드

> 이 레포에 스킬을 추가하거나 수정할 때 따라야 할 절차를 안내합니다.

## 브랜치 전략

```
main      ← 사용자가 pull 받아 사용하는 안정 브랜치
  ↑ PR (summarize-pr 권장)
develop   ← 개발 기준 브랜치
  ↑ PR (create-pr 권장)
topic     ← 작업 브랜치 (브랜치명 자유)
```

1. `develop`에서 브랜치를 만들어 작업합니다. 브랜치 이름은 자유입니다.
2. 작업이 끝나면 `develop`으로 PR을 올립니다.
3. `develop`에 충분히 쌓이면 `develop → main` PR을 만들어 머지합니다.

## 권장 스킬

이 레포의 git 스킬을 사용하면 커밋/PR 작성 규칙을 자동으로 따를 수 있습니다.

| 시점 | 스킬 | 설명 |
|------|------|------|
| 커밋 | [`commit`](./git/commit/SKILL.md) | 프로젝트 컨벤션에 맞는 커밋 메시지 생성 |
| PR 생성 | [`create-pr`](./git/create-pr/SKILL.md) | develop 브랜치로 PR 작성 |
| main 머지 | [`summarize-pr`](./git/summarize-pr/SKILL.md) | develop → main 머지 PR 요약 작성 |

## 스킬 추가 절차

### 1. 브랜치 생성

```bash
git checkout develop
git pull origin develop
git checkout -b my-branch
```

### 2. 스킬 디렉토리 생성

[Agent Skills 스펙](https://agentskills.io/specification)을 따릅니다.

```
카테고리/
  스킬이름/
    SKILL.md          # 필수
    references/       # 참조 문서 (필요 시)
    scripts/          # 실행 스크립트 (필요 시)
    assets/           # 템플릿, 데이터 (필요 시)
```

디렉토리 이름 = `SKILL.md`의 `name` 필드. 소문자, 하이픈만 허용합니다.

### 3. SKILL.md 작성

```markdown
---
name: my-skill
description: 스킬이 하는 일과 언제 쓰는지를 함께 기술합니다.
---

# My Skill

(스킬 본문)
```

작성 규칙:

- `name`: 1~64자, 소문자 영숫자 + 하이픈, 디렉토리명과 일치
- `description`: 1~1024자, **무엇을 하는지 + 언제 쓰는지** 포함
- frontmatter에 `name`, `description`, `license`, `compatibility`, `metadata`, `allowed-tools` 외 필드 금지
- 스킬 본문(지시사항)은 **영어**로 작성 (토큰 효율)
- 사용자에게 보이는 출력 메시지는 **한국어**로 작성
- 500줄이 넘으면 상세 내용을 `references/`로 분리

### 4. 커밋 & PR

`commit` 스킬과 `create-pr` 스킬 사용을 권장합니다.

```bash
# Claude Code에서
/commit        # 컨벤션에 맞는 커밋 메시지 자동 생성
/create-pr     # develop 대상 PR 생성
```

### 5. main에 반영

`develop`에 변경사항이 충분히 쌓이면 `develop → main` PR을 만듭니다. `summarize-pr` 스킬을 사용하면 포함된 커밋들을 자동으로 요약해 PR 본문을 작성합니다.

```bash
/summarize-pr  # develop → main PR 요약 생성
```

머지하면 사용자들이 `main`을 pull 받아 새 스킬을 쓸 수 있습니다.

## 기존 스킬 수정

1. `develop`에서 브랜치를 만들어 해당 `SKILL.md`나 `references/`를 수정합니다.
2. 스킬의 `name`이나 `description`이 바뀌었다면 `/update-readme`를 실행해 `README.md`를 갱신합니다.
3. `develop`으로 PR을 올립니다.

## 가이드 문서 추가/수정

루트의 `*-guide.md` 파일은 사용자 대상 안내 문서입니다.

- 파일명: `{주제}-guide.md`
- 추가/수정 후 `/update-readme`를 실행해 README 가이드 테이블을 갱신합니다.

## 체크리스트

PR을 올리기 전에 확인합니다.

- [ ] `SKILL.md`의 `name`이 디렉토리명과 일치하는가
- [ ] `description`에 "무엇을 하는지"와 "언제 쓰는지"가 포함되어 있는가
- [ ] frontmatter에 허용되지 않는 커스텀 필드가 없는가
- [ ] 스킬 지시사항은 영어, 출력 메시지는 한국어로 작성했는가
- [ ] 스킬이 추가/삭제/이름 변경된 경우 `/update-readme`를 실행했는가
