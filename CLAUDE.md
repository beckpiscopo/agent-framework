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
│   └── requirements.txt
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
python -m venv venv && source venv/bin/activate
pip install -r requirements.txt
uvicorn app.main:app --reload --port 8000

# Frontend
cd frontend
npm install
npm run dev

# Testing (Backend)
cd backend
pytest tests/ -v                              # All tests
pytest tests/unit/ -v                         # Unit tests only
pytest --cov=app                              # With coverage

# Testing (Frontend)
cd frontend
npm test                                      # Vitest
npx playwright test                           # E2E tests

# Database (Supabase)
supabase start                                # Local dev
supabase db push                              # Push migrations
supabase db pull                              # Pull remote changes
```

## Reference Documentation

Read these documents when working on specific areas:

| Document | When to Read |
|----------|--------------|
| `.claude/PRD.md` | Understanding requirements, features, API spec |
| `.claude/reference/api.md` | Building API endpoints, Pydantic schemas, FastAPI patterns |
| `.claude/reference/components.md` | React components, hooks, state management, forms |
| `.claude/reference/testing.md` | Unit/integration/E2E testing patterns |
| `.claude/reference/deploy.md` | Supabase setup, Vercel/Railway deployment, CI/CD |

**Implementation plans** live in `.agents/plans/` - create one per feature during the PLAN phase.

## Code Conventions

### Backend (Python)
- Use Pydantic models for all request/response schemas
- Separate schemas: `UserCreate`, `UserUpdate`, `UserResponse`
- Use `Depends()` for database sessions and shared dependencies
- Services contain business logic, routers are thin
- Use HTTPException with appropriate status codes

### Frontend (React/Next.js)
- App Router with file-based routing
- Use TanStack Query for all API calls (no raw useEffect fetching)
- Tailwind CSS for styling - no separate CSS files
- Forms with react-hook-form + Zod validation
- Zustand for client-side state, React Query for server state

### API Design
- RESTful endpoints under `/api/`
- Return 201 for POST (created), 204 for DELETE
- Use HTTPException with descriptive error messages
- Validate all input with Pydantic

### Git
- Conventional commits: `type(scope): description`
- Types: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`
- Keep commits atomic (one logical change per commit)

## Database (Supabase)

Connection via SQLAlchemy using Supabase's PostgreSQL:

```python
# app/database.py
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
import os

DATABASE_URL = os.getenv("DATABASE_URL")
engine = create_engine(DATABASE_URL)
SessionLocal = sessionmaker(bind=engine)
```

Supabase also provides:
- **Auth**: Use `supabase.auth` for user management (optional)
- **Storage**: Use for file uploads
- **Realtime**: Subscribe to database changes

## Testing Strategy

### Testing Pyramid
- **70% Unit tests**: Pure functions, business logic, validators
- **20% Integration tests**: API endpoints with test database
- **10% E2E tests**: Critical user journeys with Playwright

### Test Organization
```
tests/
├── conftest.py              # Shared fixtures
├── unit/                    # Business logic tests
├── integration/             # API tests with real DB
└── e2e/                     # Playwright user journey tests
```

## Slash Commands

Available commands in `.claude/commands/`:

| Command | Description |
|---------|-------------|
| `/plan-project` | Plan a new project from a rough idea, generate PRD |
| `/create-plan` | Create implementation plan for a feature/phase |
| `/commit` | Smart commit with conventional format |
| `/review` | Code review for quality and bugs |

## Evolving This Document

When you encounter a pattern that should be standardized:
1. Add detailed patterns to the appropriate `.claude/reference/` doc
2. Add a summary here if it's a global convention
3. Keep this document scannable - details go in reference docs
