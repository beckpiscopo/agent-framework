# Architecture Reference

<!--
  This document is the living architecture reference for the project.
  Claude must update this file whenever the system architecture changes.

  Update triggers:
  - Adding/removing backend services, models, or routers
  - Adding/removing frontend pages, components, or hooks
  - Changing the data pipeline or processing stages
  - Adding/removing database tables, indexes, or extensions
  - Adding/removing API endpoints
  - Changing scheduled jobs or background tasks
  - Adding/removing external dependencies
-->

Comprehensive system architecture for [Project Name]. Last updated: [date].

---

## System Overview

```
[High-level data flow diagram]
[Show: data sources → ingestion → storage → processing → API → frontend]
```

---

## Data Pipeline

### Stage 1: Ingestion

<!-- List all data sources with their service files, refresh intervals, and target tables -->

| Source | Service | Interval | Data Type |
|--------|---------|----------|-----------|
| | | | |

### Stage 2: Processing

<!-- Describe any processing that happens after ingestion (NLP, enrichment, normalization, etc.) -->

```
[Processing flow diagram]
```

### Stage 3: Output

<!-- Describe how processed data is served (scoring, aggregation, caching, etc.) -->

```
[Output flow diagram]
```

---

## Database Schema

<!-- List all tables grouped by purpose -->

### Core Tables

| Table | Purpose | Key Columns | Notes |
|-------|---------|-------------|-------|
| | | | |

### Indexes

<!-- List important indexes, especially composite or specialized ones -->

### Extensions

<!-- List any PostgreSQL extensions or database-specific features -->

---

## Service Layer

### Ingestion Services

| Service | File | Writes To |
|---------|------|-----------|
| | | |

### Processing Services

| Service | File | Reads From | Writes To |
|---------|------|------------|-----------|
| | | | |

### Infrastructure

| Service | File | Purpose |
|---------|------|---------|
| | | |

---

## API Endpoints

All under `/api/`. FastAPI with Pydantic response models.

<!-- Group endpoints by domain. Include method, router file, and description. -->

### Core

| Endpoint | Method | Router | Description |
|----------|--------|--------|-------------|
| `/api/health` | GET | system.py | Health check |
| | | | |

---

## Frontend Architecture

### Pages (App Router)

| Route | Page | Purpose |
|-------|------|---------|
| `/` | Dashboard | |
| | | |

### Component Organization

```
components/
├── dashboard/
├── layout/
└── ui/
```

### Data Flow (Frontend)

```
TanStack Query hooks ──> fetchApi() ──> /api/* ──> JSON response
       |
       v
  Component props / state
       |
       v
  Rendering
```

- **Server state**: TanStack Query
- **Client state**: Zustand stores
- **Styling**: Tailwind CSS

### Hooks Inventory

| Hook File | Hooks | Refetch |
|-----------|-------|---------|
| | | |

---

## Scheduled Jobs

<!-- List all background/scheduled jobs -->

| # | Job | Service | Interval | Writes To |
|---|-----|---------|----------|-----------|
| | | | | |

---

## Key Dependencies

### Backend
- FastAPI + Uvicorn
- SQLAlchemy 2.0
- [Add project-specific dependencies]

### Frontend
- Next.js 14+ (App Router)
- React 18+
- TanStack Query
- Tailwind CSS
- [Add project-specific dependencies]

### Infrastructure
- PostgreSQL (via Docker)
- [Add project-specific infrastructure]
