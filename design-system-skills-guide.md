# 디자인 시스템 마이그레이션 스킬 가이드 (Claude Code)

> **이 문서는 사용자(개발자)를 위한 가이드입니다.**
> 기존 프로젝트를 @l-fin/ui-components 디자인 시스템으로 마이그레이션하기 위한 AI 스킬 사용법을 안내합니다.
> 다른 AI 에이전트(Codex, Cursor 등)를 사용한다면 [openskills 가이드](./openskills-guide.md)를 참고하세요.

## 제공 스킬

| 스킬 | 설명 | 실행 순서 |
|------|------|:---------:|
| `migrate-plan` | prev/ 폴더 분석 후 마이그레이션 계획 파일 생성 | 1 (선택) |
| `migrate` | 기존 프로젝트를 @l-fin/ui-components로 마이그레이션 | 2 |
| `apply-composites` | AppLayout, DataTable, FilterBuilder 고급 컴포넌트 적용 | 3 |

---

## 마이그레이션 흐름

두 가지 모드가 있습니다.

### Mode A: 기존 프로젝트에 라이브러리 추가

이미 동작 중인 프로젝트에 @l-fin/ui-components를 설치하고 기존 UI 라이브러리를 교체합니다.

```
/migrate                    → 프로젝트 분석 + 마이그레이션 실행
/migrate src/components     → 특정 폴더만 마이그레이션
/apply-composites           → 고급 컴포넌트 적용
```

### Mode B: create-lfin-app 기반 마이그레이션

새 프로젝트를 생성하고, 기존 코드를 prev/ 폴더에 넣어 계획 기반으로 마이그레이션합니다.

```
npx create-lfin-app my-app        → 새 프로젝트 생성
cp -r ./old-project ./my-app/prev → 기존 코드를 prev/ 에 복사
cd my-app
/migrate-plan                     → prev/ 분석 → MIGRATION_PLAN.md 생성
/migrate                          → 계획 기반 마이그레이션 실행
/apply-composites                 → 고급 컴포넌트 적용
```

---

## 사전 요구사항

### Node.js 22+

```bash
node -v   # v22.x.x 이상 필요
```

### GitHub Packages 인증

@l-fin/ui-components는 GitHub Packages에 호스팅되어 있어 인증 설정이 필요합니다.
`/migrate` 실행 시 설정 여부를 확인하고, 미설정 시 안내합니다.

> Mode B(create-lfin-app)로 생성한 프로젝트는 이미 설정되어 있으므로 이 단계를 건너뜁니다.

---

## 설치 방법

```bash
# 전체 복사 (권장)
cp -r /path/to/lfin-ai-toolkit/admin-design-system .claude/skills/admin-design-system
```

개별 스킬만 필요한 경우:

```bash
cp -r /path/to/lfin-ai-toolkit/admin-design-system/migrate .claude/skills/migrate
cp -r /path/to/lfin-ai-toolkit/admin-design-system/migrate-plan .claude/skills/migrate-plan
cp -r /path/to/lfin-ai-toolkit/admin-design-system/apply-composites .claude/skills/apply-composites
```

---

## 스킬별 상세

### migrate-plan

기존 프로젝트(prev/)를 분석해서 마이그레이션 계획 파일(`MIGRATION_PLAN.md`)을 생성합니다.

```bash
/migrate-plan
/migrate-plan --output ./plans/v1.md    # 커스텀 출력 경로
/migrate-plan --prev ./old-project      # 커스텀 prev 경로
```

감지하는 라이브러리: shadcn/ui, MUI, Chakra UI, Ant Design, Bootstrap, Mantine, lucide-react, react-icons, react-hook-form, Next.js (App/Pages Router), Redux, Redux Saga, styled-components, moment.js, sonner 등

### migrate

프로젝트를 분석하고 @l-fin/ui-components로 마이그레이션합니다.

```bash
/migrate                    # 전체 마이그레이션
/migrate --analyze-only     # 분석만 (변경 없음)
/migrate --dry-run          # 미리보기
/migrate src/pages          # 특정 폴더만
```

마이그레이션 항목: UI 컴포넌트, 폼, 아이콘, 라우팅, 상태관리, 스타일링, CSS 변수, 테마 등

### apply-composites

기본 마이그레이션 후 고급 Composite 컴포넌트를 적용합니다.

```bash
/apply-composites                              # 전체 적용
/apply-composites --app-layout                 # AppLayout만
/apply-composites --datatable                  # DataTable만
/apply-composites --filter-builder             # FilterBuilder만
/apply-composites --app-layout --datatable     # 조합 가능
```

| 컴포넌트 | 설명 |
|----------|------|
| AppLayout | 사이드바 + 헤더 통합 레이아웃 (리사이즈, 모바일 대응, 다크모드) |
| DataTable | TanStack Table 기반 데이터 테이블 (가상 스크롤, 정렬, 페이지네이션) |
| FilterBuilder | 선언적 필터 폼 생성기 (URL 동기화, DataTable 연동) |

> `apply-composites`는 반드시 `migrate` 실행 후 사용하세요.

---

## 마이그레이션 결과

각 스킬 실행 후 결과 파일이 프로젝트 루트에 생성됩니다.

| 스킬 | 결과 파일 |
|------|-----------|
| `migrate-plan` | `MIGRATION_PLAN.md` |
| `migrate` | `MIGRATION_RESULT_{날짜}.md` |
| `apply-composites` | `APPLY_COMPOSITES_RESULT_{날짜}.md` |

## 참고 문서

- @l-fin/ui-components 문서: `https://admin-template.lpin.io/llms/llms.txt`
- 디자인 토큰: `https://admin-template.lpin.io/llms/reference/css-variables.md`
- CRUD 패턴: `https://admin-template.lpin.io/llms/patterns/crud.md`
