# Migration Verification

Run verification after migration. Use detected package manager (`{pm}`).

## Common Checks

1. **Type Check** (TypeScript projects)

   ```bash
   tsc -b
   ```

2. **Lint Check**

   ```bash
   prettier --write . && eslint --fix
   ```

3. **Dev Server**

   ```bash
   {pm} dev
   # or
   {pm} dev:local
   # or
   {pm} dev:dev
   ```

   - Verify no runtime errors
   - **IMPORTANT**: Terminate after verification

4. **Development Build**
   ```bash
   {pm} build
   # or
   {pm} build:local
   # or
   {pm} build:dev
   ```

## On Error

- If type/lint errors: fix before proceeding
- If runtime errors: check console, rollback if needed (`git checkout .`)
- Report errors to user in Korean
