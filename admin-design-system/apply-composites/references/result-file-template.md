# Apply Composites Result File Template

Create result file in project root after migration.

**Filename Format**: `APPLY_COMPOSITES_RESULT_{YYYY_MM_DD}.md`

## Template

```markdown
# Composite 컴포넌트 적용 결과

> 실행일: {YYYY-MM-DD HH:mm}
> 적용 옵션: --datatable / --filter-builder / --app-layout / (전체)

## 요약

| 항목              | 값                                  |
| ----------------- | ----------------------------------- |
| 총 변경 파일 수   | {count}                             |
| 마이그레이션 성공 | {count}                             |
| 스킵/미적용       | {count}                             |
| 수행 시간         | {HH:mm:ss} (약 N분)                 |
| 토큰 사용량 🔸    | ~{input}K input / ~{output}K output |

> 🔸 토큰 사용량은 대략적인 추정치입니다.

## 변경된 파일

### AppLayout

| 파일                    | 변경 내용            | 상태 |
| ----------------------- | -------------------- | ---- |
| `src/routes/__root.tsx` | AppShell → AppLayout | ✅   |

### DataTable

| 파일                            | 변경 내용                 | 상태 |
| ------------------------------- | ------------------------- | ---- |
| `src/components/UsersTable.tsx` | useReactTable → DataTable | ✅   |

### FilterBuilder

| 파일                               | 변경 내용                   | 상태 |
| ---------------------------------- | --------------------------- | ---- |
| `src/components/SearchFilters.tsx` | Custom form → FilterBuilder | ✅   |

## 미마이그레이션 파일

| 파일                              | 사유                              |
| --------------------------------- | --------------------------------- |
| `src/components/ComplexTable.tsx` | 커스텀 로직이 많아 수동 검토 필요 |

## 검증 결과

| 검증 항목 | 결과              |
| --------- | ----------------- |
| 타입 체크 | ✅ 통과 / ❌ 실패 |
| 린트      | ✅ 통과 / ❌ 실패 |
| 빌드      | ✅ 통과 / ❌ 실패 |

## 다음 단계

- [ ] 미마이그레이션 파일 수동 검토
- [ ] `{pm} dev` 실행하여 UI 확인
- [ ] 변경사항 커밋
```
