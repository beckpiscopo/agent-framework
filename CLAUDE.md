# [Project Name] - Complete Documentation

<!-- Replace with your project description -->
A brief description of what this project does.

## Tech Stack

- **Backend**: Python 3.11+, FastAPI, SQLAlchemy, Supabase (PostgreSQL)
- **Frontend**: Next.js 14+, React 18, Tailwind CSS, TanStack Query
- **Testing**: pytest, Vitest, Playwright
- **Infrastructure**: Supabase (DB + Auth + Storage), Vercel, Railway

## Project Structure

```
project/
├── backend/
│   ├── app/
│   │   ├── main.py           # FastAPI entry point
│   │   ├── config.py         # Settings and environment
│   │   ├── database.py       # Supabase/SQLAlchemy connection
│   │   ├── models/           # SQLAlchemy models
│   │   ├── schemas/          # Pydantic schemas
│   │   ├── routers/          # API endpoints
│   │   ├── services/         # Business logic
│   │   └── utils/            # Helper functions
│   └── pyproject.toml
├── frontend/
│   ├── src/
│   │   ├── app/              # Next.js App Router
│   │   ├── components/       # React components
│   │   ├── hooks/            # Custom hooks
│   │   ├── lib/              # Utilities, API client
│   │   └── stores/           # Zustand stores
│   └── package.json
├── tests/
│   ├── unit/                 # Unit tests
│   ├── integration/          # API integration tests
│   └── e2e/                  # Playwright E2E tests
├── .claude/
│   ├── commands/             # Slash commands
│   ├── reference/            # Best practices docs
│   └── PRD.md                # Product requirements
└── .agents/
    └── plans/                # Implementation plans (per feature)
```

## Commands

```bash
# Backend
cd backend
uv sync
uv run uvicorn app.main:app --reload --port 8000

# Frontend
cd frontend
npm install
npm run dev

# Testing
uv run pytest tests/ -v                           # Backend tests
npm test                                          # Frontend unit tests
npx playwright test                               # E2E tests

# Database (Supabase)
supabase start                                    # Local dev
supabase db diff -f migration_name                # Create migration
supabase db push                                  # Apply migrations
```

## Reference Documentation

Read these documents when working on specific areas:

| Document | When to Read |
|----------|--------------|
| `.claude/PRD.md` | Understanding requirements, features, API spec |
| `.claude/reference/architecture.md` | System architecture, data pipeline, database schema, API map, frontend structure |
| `.claude/reference/data-dictionary.md` | External data sources, APIs, ingestion schedules, auth requirements |
| `.claude/reference/metric-dictionary.md` | Indicators, scoring weights, normalization methods, tier definitions |
| `.claude/reference/api-best-practices.md` | Building API endpoints, Pydantic schemas, FastAPI patterns |
| `.claude/reference/components.md` | React components, hooks, state management, forms |
| `.claude/reference/testing.md` | Unit/integration/E2E testing patterns |
| `.claude/reference/deployment-best-practices.md` | Supabase setup, Vercel/Railway deployment, CI/CD |

**Implementation plans** live in `.agents/plans/` - create one per feature during the PLAN phase.

## MCP Integrations

```bash
claude mcp add playwright-mcp    # Browser automation, E2E testing
```

## Code Conventions

### Living Documentation (MANDATORY)

Claude must keep these three reference documents up to date as the project evolves:

- **Architecture** (`.claude/reference/architecture.md`): Update when adding/removing services, models, routers, endpoints, pages, hooks, database tables, scheduled jobs, or dependencies.
- **Data Dictionary** (`.claude/reference/data-dictionary.md`): Update when adding/removing/modifying data sources, changing refresh intervals, or changing which modules a source feeds. If the project has a frontend data dictionary page, keep `frontend/src/lib/data-dictionary.ts` in sync.
- **Metric Dictionary** (`.claude/reference/metric-dictionary.md`): Update when adding/removing indicators, changing normalization methods, modifying scoring weights or tier thresholds, or changing alert trigger conditions.

Each document has HTML comments at the top listing specific update triggers. Read these before making changes.

### Backend (Python)
- Use Pydantic models for all request/response schemas
- Separate schemas: `UserCreate`, `UserUpdate`, `UserResponse`
- Use `Depends()` for database sessions and shared dependencies
- Services contain business logic, routers are thin
- Structured logging with structlog

### Frontend (React/Next.js)
- App Router with file-based routing
- TanStack Query for all API calls (no raw useEffect fetching)
- Tailwind CSS for styling - no separate CSS files
- Forms with react-hook-form + Zod validation
- Zustand for client-side state, React Query for server state

### API Design
- RESTful endpoints under `/api/`
- Return 201 for POST (created), 204 for DELETE
- Use HTTPException with descriptive error messages

### Git
- Conventional commits: `type(scope): description`
- Types: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`

## Database (Supabase)

- Connection via SQLAlchemy using Supabase's PostgreSQL
- **Auth**: Use `supabase.auth` for user management
- **Storage**: Use for file uploads
- **Realtime**: Subscribe to database changes

## Testing Strategy

- **70% Unit tests**: Pure functions, business logic, validators
- **20% Integration tests**: API endpoints with test database
- **10% E2E tests**: Critical user journeys with Playwright

## Slash Commands

| Command | Description |
|---------|-------------|
| `/plan-project` | Plan a new project from a rough idea, generate PRD |
| `/create-plan` | Create implementation plan for a feature/phase |
| `/build` | Read plan, implement next incomplete step, validate, commit |
| `/status` | Show progress, current step, environment health |
| `/validate` | Run validation checks for current/specific step |
| `/briefing` | Generate a situation briefing from live backend data |
| `/commit` | Smart commit with conventional format |
| `/review` | Code review for quality and bugs |

## Evolving This Document

When you encounter a pattern that should be standardized:
1. Add detailed patterns to the appropriate `.claude/reference/` doc
2. Add a summary here if it's a global convention
3. Keep this document scannable - details go in reference docs
