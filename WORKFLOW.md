# Agentic Development Workflow

> How to get the best results from AI-assisted coding

## The Core Problem

After many messages, AI coding agents:
- Accumulate stale context
- Repeat earlier mistakes
- Make assumptions based on outdated information
- Lose focus on the actual goal

**Solution: The Context Reset**

---

## The Context Reset Pattern

### Phase 1: PLAN (Research & Design)

In a fresh conversation:

1. **State your goal clearly**
   > "I want to add user authentication to this app"

2. **Have Claude explore the codebase**
   > "Read the PRD.md and relevant .agents/reference docs. Explore the existing code structure."

3. **Discuss approaches**
   > "What are our options? What are the tradeoffs?"

4. **Capture the plan**
   > "Write a detailed implementation plan to a new file: docs/plans/auth-implementation.md"

**Exit this conversation. Do not start implementing.**

---

### Phase 2: DOC (Capture Everything)

The plan document should contain:

```markdown
# [Feature] Implementation Plan

## Goal
[What we're building and why]

## Approach
[The chosen approach and why]

## Files to Create/Modify
- path/to/file.py - [what changes]
- path/to/other.tsx - [what changes]

## Implementation Steps
1. [First step with details]
2. [Second step with details]
3. ...

## Edge Cases / Considerations
- [Thing to watch out for]
- [Another consideration]

## Testing Strategy
- [How we'll verify this works]
```

---

### Phase 3: EXEC (Fresh Implementation)

Start a **new conversation**:

1. **Load the plan**
   > "Read docs/plans/auth-implementation.md and implement it step by step."

2. **Let Claude work through the steps**
   - The plan provides clear direction
   - No accumulated confusion from planning discussions
   - Fresh context = better code

3. **If context degrades**, start another fresh conversation
   > "Continue from step 5 of docs/plans/auth-implementation.md"

---

## When to Reset Context

Reset when you notice:
- Claude repeating mistakes it already "fixed"
- Confusion about what's been done vs. what's planned
- Unnecessary complexity creeping in
- The conversation is getting long (50+ messages)

---

## The System Evolution Mindset

Every friction point is an opportunity to improve:

### Bug: Claude uses wrong import style
**Fix:** Add to `.agents/reference/api.md`:
```markdown
## Import Conventions
Always use relative imports within the app package:
- `from app.models import User` (not `from backend.app.models`)
```

### Bug: Claude forgets to run tests
**Fix:** Add to `CLAUDE.md`:
```markdown
## After Making Changes
Always run the relevant tests:
- Backend: `pytest tests/`
- Frontend: `npm test`
```

### Bug: Claude doesn't understand auth flow
**Fix:** Create `.agents/reference/auth.md` with detailed auth patterns

---

## Quick Reference

| Situation | Action |
|-----------|--------|
| Starting new feature | Create plan doc first |
| Long conversation | Reset and continue from plan |
| Claude making repeated mistakes | Add rule to prevent it |
| Complex domain logic | Create reference doc explaining it |
| Repetitive task | Create a `/command` for it |

---

## File Checklist

For every project using this workflow:

- [ ] `PRD.md` - Requirements and feature phases
- [ ] `CLAUDE.md` - Global rules
- [ ] `.agents/AGENTS.md` - Context loader
- [ ] `.agents/reference/*.md` - Domain-specific patterns
- [ ] `.claude/commands/*.md` - Reusable workflows
- [ ] `docs/plans/*.md` - Implementation plans (created as needed)
