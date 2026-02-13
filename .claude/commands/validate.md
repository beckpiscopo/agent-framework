# Validate Current Step

Run the validation checks for the most recently completed (or in-progress) step from the implementation plan.

## Arguments

$ARGUMENTS - Optional: specific step number to validate (e.g., "step 5"), or "all" to validate all completed steps (default: most recent step with completed tasks)

## Process

### Step 1: Identify What to Validate

1. Read the active plan from `.agents/plans/`
2. If `$ARGUMENTS` specifies a step number, validate that step
3. If `$ARGUMENTS` is "all", validate every completed step in order
4. Otherwise, find the most recent step that has completed tasks (the current working step)
5. Extract the **Validation** section for the target step(s)

### Step 2: Run Validation Commands

For each validation item:
1. Execute the command or check described
2. Capture the output
3. Determine pass/fail based on expected outcome described in the plan

### Step 3: Run Tests

If the step has associated tests mentioned in the plan's Testing Checklist:
1. Run the specific test files: `uv run pytest [test_file] -v`
2. If frontend tests exist for the step: `cd frontend && npm test -- [test_file]`
3. Capture results

### Step 4: Check Code Quality

Quick checks on files modified in this step:
- Python: `uv run python -m py_compile [file]` for syntax check
- TypeScript: `cd frontend && npx tsc --noEmit` for type checking
- No import errors (attempt to import key modules)

### Step 5: Report Results

Format output as:

```
## Validation: Step N — [Step Name]

### Checks
✅ [validation item 1] — passed
✅ [validation item 2] — passed
❌ [validation item 3] — FAILED
   Error: [error details]
   Expected: [what was expected]
   Got: [what actually happened]

### Tests
✅ tests/unit/test_scoring.py — 12/12 passed
❌ tests/integration/test_gdelt.py — 3/5 passed
   FAILED: test_event_categorization — AssertionError: ...
   FAILED: test_deduplication — ...

### Code Quality
✅ All Python files compile
✅ TypeScript type check passed

### Result: PASS / FAIL
[If FAIL]: Issues to fix before proceeding:
1. [Issue 1 and suggested fix]
2. [Issue 2 and suggested fix]

[If PASS]: Step N is validated. Run `/build` to continue.
```

### Step 6: Fix Issues (if any)

If validation fails and the fixes are straightforward:
> "Validation found N issues. Want me to fix them now?"

If the user says yes:
1. Fix each issue
2. Re-run validation
3. Report updated results

## Special: Validate All

When `$ARGUMENTS` is "all":
- Run validation for every completed step, in order
- This is useful when starting a new session to verify the codebase is in good shape
- Report a summary table:

```
## Full Validation Summary

| Step | Name | Status |
|------|------|--------|
| 1 | Docker + Database | ✅ PASS |
| 2 | Backend Scaffolding | ✅ PASS |
| 3 | SQLAlchemy Models | ❌ FAIL (1 issue) |
| 4+ | Not yet implemented | ⬜ SKIP |

Overall: 2/3 validated steps passing
```
