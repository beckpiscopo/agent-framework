# Product Requirements Document (PRD)

> **This is your source of truth.** Every AI conversation should reference this document.
> Update it as requirements evolve.

## Project Overview

**Project Name:** [Your Project Name]

**Description:** [One-paragraph description of what you're building]

**Problem Statement:** [What problem does this solve? Who has this problem?]

---

## Tech Stack

### Backend
- **Framework:** FastAPI
- **Database:** PostgreSQL with SQLAlchemy ORM
- **Auth:** JWT tokens
- **API Style:** RESTful

### Frontend
- **Framework:** Next.js 14+ (App Router)
- **Styling:** Tailwind CSS
- **State:** React Query for server state, Zustand for client state
- **Forms:** React Hook Form + Zod validation

### Infrastructure
- **Hosting:** [Vercel/Railway/AWS/etc.]
- **CI/CD:** GitHub Actions
- **Monitoring:** [Your choice]

---

## Feature Phases

### Phase 1: Foundation (MVP)

#### 1.1 User Authentication
- [ ] User registration with email/password
- [ ] Login/logout functionality
- [ ] Password reset flow
- [ ] JWT token management
- [ ] Protected route middleware

**Success Criteria:** Users can create accounts, log in, and access protected resources.

#### 1.2 Core Data Model
- [ ] Define database schema
- [ ] Create SQLAlchemy models
- [ ] Set up Alembic migrations
- [ ] Seed data for development

**Success Criteria:** Database is properly structured and migrations run cleanly.

#### 1.3 Basic UI Shell
- [ ] Layout component with navigation
- [ ] Auth pages (login, register, forgot password)
- [ ] Dashboard skeleton
- [ ] Responsive design

**Success Criteria:** Users can navigate the app and see a polished interface.

---

### Phase 2: Core Features

#### 2.1 [Feature Name]
- [ ] [Requirement 1]
- [ ] [Requirement 2]
- [ ] [Requirement 3]

**Success Criteria:** [What does "done" look like?]

#### 2.2 [Feature Name]
- [ ] [Requirement 1]
- [ ] [Requirement 2]

**Success Criteria:** [What does "done" look like?]

---

### Phase 3: Polish & Scale

#### 3.1 Performance
- [ ] Database query optimization
- [ ] API response caching
- [ ] Frontend bundle optimization
- [ ] Image optimization

#### 3.2 Observability
- [ ] Error tracking setup
- [ ] Performance monitoring
- [ ] Logging infrastructure
- [ ] Alerting rules

---

## API Endpoints (Draft)

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/auth/register` | Create new user |
| POST | `/api/auth/login` | Authenticate user |
| POST | `/api/auth/refresh` | Refresh JWT token |
| GET | `/api/users/me` | Get current user |
| ... | ... | ... |

---

## Data Models (Draft)

### User
```
- id: UUID (PK)
- email: string (unique)
- password_hash: string
- created_at: timestamp
- updated_at: timestamp
```

### [Other Models]
```
[Define your models here]
```

---

## Non-Functional Requirements

- **Response Time:** API responses < 200ms p95
- **Availability:** 99.9% uptime
- **Security:** OWASP top 10 compliance
- **Accessibility:** WCAG 2.1 AA compliance

---

## Open Questions

> Track decisions that need to be made here

- [ ] [Question 1]
- [ ] [Question 2]

---

## Changelog

| Date | Change | Author |
|------|--------|--------|
| YYYY-MM-DD | Initial PRD created | [Name] |
