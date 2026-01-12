# Project Rules

> Global rules for Claude Code. These apply to all tasks.

## Quick Start

Before starting any task:
1. Read `PRD.md` to understand project requirements
2. Read `.agents/AGENTS.md` to load relevant context for your task

## Project Overview

**Stack:** FastAPI (backend) + Next.js (frontend)

**Structure:**
```
/backend    - FastAPI application
/frontend   - Next.js application
/docs       - Additional documentation
```

## Global Conventions

### Code Style
- Write clean, readable code over clever code
- Use descriptive variable/function names
- Keep functions small and focused (single responsibility)
- Add comments only when the "why" isn't obvious

### Git
- Use conventional commits: `type(scope): description`
- Keep commits atomic (one logical change per commit)
- Write meaningful commit messages

### Security
- Never commit secrets or credentials
- Validate all user input
- Use parameterized queries (no raw SQL)
- Sanitize output to prevent XSS

### Error Handling
- Handle errors at appropriate boundaries
- Provide meaningful error messages
- Log errors with context for debugging

## On-Demand Context

For detailed patterns, load the relevant reference doc:

| Working On | Load This |
|------------|-----------|
| Backend/API | `.agents/reference/api.md` |
| Frontend/Components | `.agents/reference/components.md` |
| Deployment | `.agents/reference/deploy.md` |
| Tests | `.agents/reference/testing.md` |

## Commands Available

- `/commit` - Smart commit with conventional format
- `/review` - Code review for quality and bugs

## Evolving These Rules

When you encounter a pattern that should be standardized:
1. Add it to the appropriate reference doc in `.agents/reference/`
2. If it's truly global, add it here
3. Keep rules actionable and specific
