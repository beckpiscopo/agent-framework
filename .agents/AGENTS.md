# Agent Context Loader

> **Load context on-demand.** Only reference the docs relevant to your current task.
> This keeps your context lean and focused.

## Project Quick Context

**Stack:** FastAPI (backend) + Next.js (frontend)

**Key Directories:**
- `/backend` - FastAPI application
- `/frontend` - Next.js application
- `/docs` - Additional documentation

---

## Reference Documents

Load these based on what you're working on:

### Working on Backend/API?
```
Read: .agents/reference/api.md
```
Contains: FastAPI patterns, database conventions, API design rules

### Working on Frontend/Components?
```
Read: .agents/reference/components.md
```
Contains: React/Next.js patterns, component structure, state management

### Working on Deployment/Infrastructure?
```
Read: .agents/reference/deploy.md
```
Contains: Environment setup, CI/CD, infrastructure notes

### Working on Tests?
```
Read: .agents/reference/testing.md
```
Contains: Test patterns, coverage requirements, mocking strategies

---

## Loading Instructions for Claude

When starting a task:

1. **Identify the domain** - Is this backend, frontend, deployment, or testing?
2. **Load relevant reference** - Read only what you need
3. **Check PRD.md** - Ensure your work aligns with requirements
4. **Proceed with context** - Now you have focused, relevant rules

### Example Prompts

**For backend work:**
> "Read .agents/reference/api.md then implement the user registration endpoint from PRD.md Phase 1.1"

**For frontend work:**
> "Read .agents/reference/components.md then build the login form from PRD.md Phase 1.3"

**For full-stack feature:**
> "Read .agents/reference/api.md and .agents/reference/components.md then implement [feature]"

---

## Adding New Reference Docs

When you identify patterns that should be documented:

1. Create a new file in `.agents/reference/`
2. Add a section to this file linking to it
3. Keep docs focused - one concern per file

Common additions:
- `security.md` - Auth patterns, input validation
- `performance.md` - Caching, optimization rules
- `integrations.md` - Third-party service patterns
