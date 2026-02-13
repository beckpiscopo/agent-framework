# Project Status

Show current progress on the implementation plan, what's done, what's next, and any blockers.

## Arguments

$ARGUMENTS - Optional: "detail" for verbose output, or a specific plan name (default: summary of active plan)

## Process

### Step 1: Load Plan

1. List files in `.agents/plans/`
2. If `$ARGUMENTS` specifies a plan, use that; otherwise use the most recent plan file
3. Read the plan file

### Step 2: Parse Progress

Count and categorize all tasks:
- **Completed** (`- [x]`): tasks that are done
- **Incomplete** (`- [ ]`): tasks still to do
- Group by step (Step 1, Step 2, etc.)
- Identify the current step (first step with any incomplete tasks)

### Step 3: Check Environment

Quick health check (don't fail if services aren't running — just report):
- Is Docker running? (`docker compose ps`)
- Is the database accessible? (`docker compose exec db pg_isready` or similar)
- Is the backend running? (`curl -s http://localhost:8000/api/health`)
- Is the frontend running? (`curl -s http://localhost:3000`)

### Step 4: Report

Format the output as:

```
## Project Status: Samson Response

**Active Plan:** .agents/plans/[plan-name].md
**Phase:** [Phase name]
**Overall Progress:** N/M steps complete (X%)

### Steps
✅ Step 1: [name] — complete
✅ Step 2: [name] — complete
🔨 Step 3: [name] — in progress (4/7 tasks done)
⬜ Step 4: [name] — pending
⬜ Step 5: [name] — pending
...

### Current Step: Step 3 — [name]
Remaining tasks:
- [ ] Task description
- [ ] Task description
- [ ] Task description

### Environment
- Docker: ✅ running / ❌ not running
- Database: ✅ connected / ❌ not reachable
- Backend: ✅ healthy / ❌ not running
- Frontend: ✅ running / ❌ not running

### Next Action
Run `/build` to continue with Step 3.
```

If `$ARGUMENTS` is "detail", also include:
- List of all files created so far (from completed steps' file tables)
- Latest composite scores from the database (if available)
- Last ingestion timestamps (if available)
- Any warnings or notes from the plan
