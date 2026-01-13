# Implementation Plans

This directory contains feature implementation plans created during the **PLAN** phase of the context reset workflow.

## How It Works

1. **PLAN phase**: Research and design in one conversation, output a detailed implementation plan here
2. **Context reset**: Close that conversation
3. **EXEC phase**: Start fresh conversation, reference the plan to implement

## Naming Convention

```
feature-name.md
```

Examples:
- `backend-foundation.md`
- `user-authentication.md`
- `dashboard-ui.md`
- `api-refactor.md`

## Plan Template

```markdown
# [Feature Name] Implementation Guide

Brief description of what this plan covers.

## Overview
- What we're building
- Why this approach was chosen

## Files to Create/Modify
- `path/to/file.py` - Description of changes
- `path/to/other.tsx` - Description of changes

## Implementation Steps

### Step 1: [Name]
Detailed instructions...

### Step 2: [Name]
Detailed instructions...

## Validation
How to verify the implementation works.

## Edge Cases
Things to watch out for.
```

## Tips

- Keep plans detailed enough that a fresh conversation can execute without asking clarifying questions
- Include specific file paths and code patterns
- Reference `.claude/reference/` docs for conventions
- Plans are living documents - update them if the approach changes
