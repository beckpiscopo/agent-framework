# Create Implementation Plan

Generate a detailed implementation plan for a feature or phase from the PRD.

## Arguments

$ARGUMENTS - Required: The feature or phase to plan (e.g., "Phase 1", "user authentication", "dashboard")

## Process

### Step 1: Load Context

1. Read `.claude/PRD.md` to understand the full project
2. Read relevant `.claude/reference/` docs for the tech stack
3. Identify the specific feature/phase from $ARGUMENTS

### Step 2: Analyze Scope

List out:
- What needs to be built
- Dependencies (what must exist first)
- Files that will be created/modified
- External integrations required

Present this to the user:
> "Here's what I understand needs to be built for [feature]. Is this complete?"

### Step 3: Break Down Tasks

Create a sequential task list where each task:
- Is independently completable
- Has clear success criteria
- Builds on previous tasks
- Can be validated before moving on

### Step 4: Generate Plan Document

Create `.agents/plans/[feature-name].md` with this structure:

```markdown
# [Feature Name] Implementation Plan

## Overview
Brief description of what this plan covers and why.

## Prerequisites
- [ ] List of things that must be done/exist first

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

## Testing Checklist
- [ ] Unit tests for [specific functionality]
- [ ] Integration tests for [specific endpoints]
- [ ] Manual testing for [specific flows]

## Edge Cases to Handle
- Edge case 1 and how to handle it
- Edge case 2 and how to handle it

## Done Criteria
How to know this feature is complete and ready.
```

### Step 5: Confirm

After generating:
> "Implementation plan created at `.agents/plans/[feature-name].md`
>
> To execute this plan in a fresh context:
> 1. Start a new Claude Code conversation
> 2. Say: 'Read `.agents/plans/[feature-name].md` and implement it step by step'
>
> Want me to make any adjustments to the plan?"
