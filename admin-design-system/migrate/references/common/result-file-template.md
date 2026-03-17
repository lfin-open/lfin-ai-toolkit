# Migration Result File Template

After migration (or `--dry-run`), create a new result file in project root.

**Filename Format**: `MIGRATION_RESULT_{YYYY_MM_DD}.md`

## Template

```markdown
# 마이그레이션 결과

> `/migrate` 실행 일시: {YYYY-MM-DD HH:mm}

## 요약

| 항목           | 값                                  |
| -------------- | ----------------------------------- |
| 모드           | `--dry-run` / `migration`           |
| 상태           | ✅ 완료 / ⚠️ 부분 완료 / ❌ 실패    |
| 수행 시간      | {HH:mm:ss} (약 N분)                 |
| 토큰 사용량 🔸 | ~{input}K input / ~{output}K output |

> 🔸 토큰 사용량은 대략적인 추정치입니다.

## 마이그레이션 대상

| 카테고리 | 현재            | 대상                 | 상태 |
| -------- | --------------- | -------------------- | ---- |
| UI       | shadcn/ui       | @l-fin/ui-components | ✅   |
| Form     | react-hook-form | @mantine/form        | ✅   |
| Icons    | lucide-react    | @tabler/icons-react  | ✅   |

## 변경 내역

### UI 컴포넌트

| 파일                     | 변경 내용          | 상태 |
| ------------------------ | ------------------ | ---- |
| `src/components/Foo.tsx` | Button, Modal 교체 | ✅   |

### 폼

| 파일                  | 변경 내용               | 상태 |
| --------------------- | ----------------------- | ---- |
| `src/pages/Login.tsx` | useForm → @mantine/form | ✅   |

### 예외 처리 필요 (수동 검토 필요)

| 파일                    | 항목        | 사유           |
| ----------------------- | ----------- | -------------- |
| `src/component/Bar.tsx` | useCombobox | re-export 안됨 |

## 검증 결과

| 검증 항목 | 결과              |
| --------- | ----------------- |
| 타입 체크 | ✅ 통과 / ❌ 실패 |
| 린트      | ✅ 통과 / ❌ 실패 |
| 개발 빌드 | ✅ 통과 / ❌ 실패 |

## 다음 단계

- [ ] 예외 처리 파일 검토
- [ ] `pnpm dev` 실행하여 UI 확인
- [ ] 변경사항 커밋
```
