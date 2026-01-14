# Deployment Reference

> Infrastructure and deployment patterns for this project

## Environments

| Environment | Purpose | URL Pattern |
|-------------|---------|-------------|
| `local` | Development | `localhost:3000` / `localhost:8000` |
| `staging` | Testing/QA | `staging.yourapp.com` |
| `production` | Live users | `yourapp.com` |

---

## Environment Variables

### Backend (.env)
```bash
# Supabase Database (get from Supabase Dashboard → Settings → Database)
DATABASE_URL=postgresql://postgres.[project-ref]:[password]@aws-0-[region].pooler.supabase.com:6543/postgres

# Supabase API (get from Supabase Dashboard → Settings → API)
SUPABASE_URL=https://[project-ref].supabase.co
SUPABASE_SERVICE_KEY=your-service-role-key  # Server-side only, never expose

# Auth (if using custom JWT alongside Supabase Auth)
SECRET_KEY=your-secret-key-min-32-chars

# CORS
ALLOWED_ORIGINS=http://localhost:3000,https://yourapp.com

# Optional
SENTRY_DSN=
LOG_LEVEL=INFO
```

### Frontend (.env.local)
```bash
NEXT_PUBLIC_API_URL=http://localhost:8000/api
NEXT_PUBLIC_APP_URL=http://localhost:3000

# Supabase (get from Supabase Dashboard → Settings → API)
NEXT_PUBLIC_SUPABASE_URL=https://[project-ref].supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key  # Safe to expose
```

**Never commit secrets.** Use `.env.example` files with placeholder values.

---

## Local Development

### Backend
```bash
cd backend
python -m venv venv
source venv/bin/activate  # or `venv\Scripts\activate` on Windows
pip install -r requirements.txt

# Run migrations
alembic upgrade head

# Start server
uvicorn app.main:app --reload --port 8000
```

### Frontend
```bash
cd frontend
npm install
npm run dev
```

### Database (Supabase)

**Option 1: Use Supabase Cloud (Recommended)**
1. Create project at [supabase.com](https://supabase.com)
2. Copy connection string from Dashboard → Settings → Database
3. Add to `.env` as `DATABASE_URL`

**Option 2: Supabase Local (for offline dev)**
```bash
# Install Supabase CLI
brew install supabase/tap/supabase

# Initialize in project root
supabase init

# Start local Supabase (runs Postgres, Auth, Storage, etc.)
supabase start

# Local connection string will be shown in output
# Default: postgresql://postgres:postgres@localhost:54322/postgres

# Stop when done
supabase stop
```

---

## Deployment

### Recommended Stack

| Service | Use For | Notes |
|---------|---------|-------|
| **Supabase** | Database, Auth, Storage | Already configured |
| **Vercel** | Frontend (Next.js) | Free tier generous |
| **Railway** | Backend (FastAPI) | Simple Python deploys |

### Supabase (Database + Auth + Storage)
Already set up! Your Supabase project provides:
- PostgreSQL database (connection string in env vars)
- Authentication (optional - can use instead of custom JWT)
- File storage (for user uploads)
- Realtime subscriptions
- Edge functions (if needed)

### Vercel (Frontend)
1. Connect GitHub repo
2. Set root directory to `frontend`
3. Add environment variables:
   - `NEXT_PUBLIC_SUPABASE_URL`
   - `NEXT_PUBLIC_SUPABASE_ANON_KEY`
   - `NEXT_PUBLIC_API_URL`
4. Deploy automatically on push

### Railway (Backend)
1. Connect GitHub repo
2. Set root directory to `backend`
3. Add environment variables:
   - `DATABASE_URL` (Supabase connection string)
   - `SUPABASE_URL`
   - `SUPABASE_SERVICE_KEY`
4. Use Dockerfile or auto-detect

---

## Monitoring & Logging

### Sentry (Error Tracking)
```python
# backend/app/main.py
import sentry_sdk

sentry_sdk.init(
    dsn=settings.SENTRY_DSN,
    traces_sample_rate=0.1,
)
```

```tsx
// frontend/src/app/layout.tsx
import * as Sentry from "@sentry/nextjs"

Sentry.init({
  dsn: process.env.NEXT_PUBLIC_SENTRY_DSN,
})
```

### Structured Logging
```python
# backend/app/config.py
import logging
import json

class JSONFormatter(logging.Formatter):
    def format(self, record):
        return json.dumps({
            "timestamp": self.formatTime(record),
            "level": record.levelname,
            "message": record.getMessage(),
            "module": record.module,
        })
```

---

## Database Migrations

### Option 1: Alembic (if using SQLAlchemy)
```bash
# Run migrations before deploying new code
alembic upgrade head

# Rollback if needed
alembic downgrade -1

# Never auto-migrate in production
# Always review migration files before running
```

### Option 2: Supabase Migrations (Recommended)
```bash
# Create a new migration
supabase migration new your_migration_name

# Edit the migration file in supabase/migrations/

# Apply to local
supabase db reset

# Push to production
supabase db push

# Pull remote schema changes
supabase db pull
```

### Supabase Dashboard
You can also manage schema directly in the Supabase Dashboard:
- Table Editor for quick changes
- SQL Editor for complex queries
- Auto-generated migrations when using the dashboard

**Tip:** For production, prefer migration files over dashboard changes for version control.
