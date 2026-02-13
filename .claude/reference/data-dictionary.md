# Data Dictionary

<!--
  This document defines all external data sources the project consumes.
  Claude must update this file whenever data sources are added, removed, or modified.

  Update triggers:
  - Adding a new data source or API integration
  - Changing refresh intervals or ingestion schedules
  - Modifying which modules/domains a source feeds
  - Adding or removing indicators derived from a source
  - Changing authentication requirements
  - Updating API endpoints or data formats

  For projects with a frontend data dictionary page, keep the TypeScript
  data-dictionary.ts file in sync with this document.
-->

## Overview

This project consumes data from the following external sources. Each source entry documents what data it provides, how it's ingested, what it feeds, and any configuration needed.

---

## Data Source Template

When adding a new data source, use this template:

```
### [Source Name]

- **ID**: `source-slug`
- **Category**: [event_data | economic | intelligence | tracking | etc.]
- **Type**: [rest_api | rss | library | scraper | static | webhook]
- **Endpoint**: [URL or description]
- **Auth**: [none | api_key | oauth | optional_key]
- **Refresh Interval**: [e.g., "15 min", "Daily 06:00 UTC", "Weekly Mon 11:00"]
- **Config Key**: [environment variable or config key, if any]

**Modules & Indicators Fed**:

| Module | Domains | Indicators |
|--------|---------|------------|
| | | |

**Notes**: [Rate limits, data format details, deduplication strategy, etc.]
```

---

## Data Sources

<!-- Add data sources below, grouped by category -->

### Event Data

<!-- Sources that provide discrete events (conflict data, news, etc.) -->

### Economic

<!-- Sources that provide financial/economic data -->

### Intelligence / NLP

<!-- Sources or local models that provide analysis, sentiment, classification -->

### Tracking

<!-- Real-time tracking sources (flight, maritime, location, etc.) -->

---

## Category Labels

<!-- Map internal category IDs to human-readable labels -->

| Category ID | Display Label |
|-------------|---------------|
| | |

---

## Frontend Integration

If the project has a frontend data dictionary page, maintain a corresponding TypeScript file at `frontend/src/lib/data-dictionary.ts` with typed source definitions. The TypeScript types should live at `frontend/src/types/data-dictionary.ts`.

### Type Definition Template

```typescript
export interface DataSource {
  id: string;
  name: string;
  description: string;
  category: string;
  sourceType: string;
  endpoint: string;
  auth: "none" | "api_key" | "optional_key";
  refreshInterval: string;
  configKey: string;
  modules: { module: string; domains: string[]; indicators: string[] }[];
  notes?: string;
}
```
