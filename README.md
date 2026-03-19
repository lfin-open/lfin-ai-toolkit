# lfin-ai-toolkit

LFIN 사내 공용 AI 스킬 모음 레포. [Agent Skills 스펙](https://agentskills.io/specification)을 따릅니다.

## 가이드 문서

| 문서 | 대상 | 설명 |
|------|------|------|
| [Git Skills 가이드](./git-skills-guide.md) | Claude Code 사용자 | Git 스킬 설치 및 사용법 |
| [디자인 시스템 스킬 가이드](./design-system-skills-guide.md) | Claude Code 사용자 | 디자인 시스템 마이그레이션 스킬 사용법 |
| [openskills 가이드](./openskills-guide.md) | 그 외 에이전트 사용자 | openskills CLI로 스킬 설치 및 사용법 |
| [Gitea 세팅 가이드](./gitea-setup-guide.md) | 전체 | Gitea REST API 로컬 환경 설정 |
| [기여 가이드](./contributing-guide.md) | 전체 | 스킬 추가/수정 절차 및 브랜치 전략 |

## 스킬 목록

### [admin-design-system/](./admin-design-system/)

| 스킬 | 설명 |
|------|------|
| [admin-design-system](./admin-design-system/SKILL.md) | 디자인 시스템 마이그레이션 스킬 세트를 안내하는 메타/카탈로그 스킬 |
| [apply-composites](./admin-design-system/apply-composites/SKILL.md) | AppLayout, DataTable, FilterBuilder 고급 컴포넌트 적용 |
| [migrate](./admin-design-system/migrate/SKILL.md) | 기존 프로젝트를 @l-fin/ui-components 디자인 시스템으로 마이그레이션 |
| [migrate-plan](./admin-design-system/migrate-plan/SKILL.md) | prev/ 폴더 분석 후 마이그레이션 계획 파일 생성 |

### [git/](./git/)

| 스킬 | 설명 |
|------|------|
| [git](./git/SKILL.md) | Git 스킬 세트를 안내하는 메타/카탈로그 스킬 |
| [commit](./git/commit/SKILL.md) | 프로젝트 컨벤션에 맞는 커밋 메시지 생성 |
| [create-pr](./git/create-pr/SKILL.md) | develop 브랜치로 PR 생성/업데이트 |
| [summarize-pr](./git/summarize-pr/SKILL.md) | develop→main 머지 PR 본문 자동 요약 |
