# Asana Start - Fetch Task and Create Plan

Start a new feature by fetching Asana task requirements and creating an implementation plan.

**Workflow Phase:** ASANA → PLAN (Phase 0 + 1)

## Arguments

$ARGUMENTS - Required: One of:
- `"next"` or `"next task"` - Find your next incomplete task (sorted by due date)
- Task name (e.g., "Backend Scaffolding", "Task 0.2")
- Task ID (e.g., "1234567890")

## Process

### Step 1: Find Asana Task

**If $ARGUMENTS is "next" or "next task":**
1. Use `asana_list_workspaces` to get workspaces
2. If multiple workspaces, ask the user which one to use (or use the first/primary)
3. Use `asana_search_tasks` with:
   - `workspace`: workspace GID from step 2
   - `assignee_any`: "me"
   - `completed`: false
   - `sort_by`: "due_date"
4. If tasks span multiple projects, group by project and show the user:
   > "Found incomplete tasks in these projects:
   > - Project A (3 tasks)
   > - Project B (5 tasks)
   > Which project should I focus on?"
5. Pick the first task (by due date) from the selected project
6. Display: "Found next task: [Task Name]"

**If $ARGUMENTS is a task name:**
1. Use `asana_list_workspaces` to get workspaces
2. If multiple workspaces, ask the user which one to use
3. Use `asana_typeahead_search` with `resource_type="task"` and `query=$ARGUMENTS` to find matching tasks
4. If multiple matches, present them to the user for selection (showing project name for context)

**If $ARGUMENTS looks like a task ID (all digits):**
1. Use `asana_get_task` directly with the task ID

### Step 2: Extract Requirements

Once task is found, retrieve:
- Task name and description
- Acceptance criteria (from description or custom fields)
- Subtasks (if any)
- Due date and assignee
- Any comments with additional context
- Dependencies or blockers

Display summary:
> "Found Asana task: [Task Name] (ID: [task_gid])
>
> **Description:**
> [task description]
>
> **Acceptance Criteria:**
> - [criterion 1]
> - [criterion 2]
>
> **Subtasks:**
> - [subtask 1]
> - [subtask 2]"

### Step 3: Create Implementation Plan

1. Read `.claude/PRD.md` to understand project context
2. Read relevant `.claude/reference/` docs for tech stack
3. Analyze the scope based on Asana requirements
4. Generate feature name from task (e.g., "backend-scaffolding")
5. Create `.agents/plans/[feature-name].md` with this structure:

```markdown
# [Feature Name] Implementation Plan

## Overview
- **Asana Task:** [Task Name] ([task_gid])
- **Asana Link:** https://app.asana.com/0/0/[task_gid]
- **Current State:** [What exists now]
- **Goal:** [What we're building and why]

## Acceptance Criteria (from Asana)
- [ ] [Criterion 1 from Asana]
- [ ] [Criterion 2 from Asana]

## Approach
[The chosen approach and why]

## Files to Create/Modify

| File | Action | Description |
|------|--------|-------------|
| `path/to/file` | Create/Modify | What changes |

## Implementation Steps

### Step 1: [Name]
**Goal:** What this step accomplishes

**Tasks:**
- [ ] Specific task 1
- [ ] Specific task 2

**Validation:**
- How to verify this step is complete

### Step 2: [Name]
...

## Edge Cases / Considerations
- [Thing to watch out for]
- [Another consideration]

## Testing Strategy
- [ ] Unit tests for [specific functionality]
- [ ] Integration tests for [specific endpoints]
- [ ] Manual testing for [specific flows]

## Done Criteria
All acceptance criteria from Asana completed + tests passing.
```

### Step 4: Confirm and Guide Next Steps

After creating the plan:
> "✅ Implementation plan created at `.agents/plans/[feature-name].md`
>
> **Next Steps (RESET → EXEC):**
> 1. Close this conversation to clear context
> 2. Start a new conversation
> 3. Say: 'Read `.agents/plans/[feature-name].md` and implement it step by step'
> 4. When complete, use `/asana-complete [task_gid]` to update Asana
>
> Want me to make any adjustments to the plan first?"

## Error Handling

- If task not found: suggest alternative search terms or list recent tasks
- If no workspace access: provide instructions to connect Asana MCP
- If task has no description: warn user and ask for verbal requirements
- If "next" returns no tasks: inform user all tasks are complete or none assigned

## Usage Examples

```bash
# Start your next task (sorted alphabetically - respects numbered tasks)
/asana-start next

# Search by task name
/asana-start Backend Scaffolding

# Search by task number/code
/asana-start Task 0.2

# Direct task ID lookup (if you have it)
/asana-start 1234567890
```
