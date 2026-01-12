# Code Review

Review code changes for quality, bugs, and improvements.

## Arguments

$ARGUMENTS - Optional: specific files or "staged" or "branch" (default: staged changes)

## Steps

1. Determine what to review:
   - If `$ARGUMENTS` is empty or "staged": review `git diff --staged`
   - If `$ARGUMENTS` is "branch": review `git diff main...HEAD`
   - Otherwise: review the specified files

2. Read the relevant reference docs based on what's being reviewed:
   - Backend code (`.py` files): Read `.agents/reference/api.md`
   - Frontend code (`.tsx`, `.ts` files): Read `.agents/reference/components.md`
   - Test files: Read `.agents/reference/testing.md`

3. Analyze the code for:
   - **Bugs**: Logic errors, edge cases, null checks
   - **Security**: Injection vulnerabilities, auth issues, data exposure
   - **Performance**: N+1 queries, unnecessary re-renders, missing indexes
   - **Code Quality**: Naming, complexity, duplication
   - **Best Practices**: Following project conventions from reference docs

4. Format the review as:

```
## Code Review Summary

### Critical Issues
- [List any blocking issues]

### Suggestions
- [List improvements]

### Positive Notes
- [What's good about this code]

### Files Reviewed
- path/to/file.py
- path/to/other.tsx
```

5. If no issues found, say so clearly

## Example Usage

- `/review` - Review staged changes
- `/review staged` - Same as above
- `/review branch` - Review all commits on current branch vs main
- `/review src/components/Button.tsx` - Review specific file
