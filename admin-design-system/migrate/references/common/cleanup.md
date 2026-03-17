# Post-Migration Cleanup

After migration is complete, identify removable items.

## Removable CSS imports (in `index.css`)

```css
/* These can be removed (included in tailwind-preset-mantine or @l-fin/ui-components/styles) */
@import '@mantine/core/styles.css'; /* included in tailwind-preset-mantine */
@import '@mantine/dates/styles.css'; /* included in @l-fin/ui-components/styles */
```

## Keep these CSS imports

```css
@import 'tailwind-preset-mantine'; /* required */
@import '@mantine/notifications/styles.css'; /* required if using notifications */
@import '@l-fin/ui-components/styles'; /* required */
@import '@l-fin/ui-components/component-styles'; /* required */
```

## User Confirmation

Ask user using `AskUserQuestion` tool:

```
질문: "마이그레이션 완료 후 정리를 진행할까요?"
옵션:
- "CSS + 패키지 삭제" - CSS import 제거 및 불필요한 패키지 삭제
- "CSS만 삭제" - CSS import만 제거
- "스킵" - 정리하지 않음
```
