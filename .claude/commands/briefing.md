# Generate Situation Briefing

Gather live data from the backend, compose a structured briefing, optionally post it to the API, and print the result.

## Arguments

$ARGUMENTS - Optional: "dry-run" to skip POSTing and just print the briefing, or a scope filter (default: all modules, POST to API if endpoint exists)

## Step 1: Understand the Project

1. Read `CLAUDE.md` and `.claude/PRD.md` to understand:
   - What modules/domains this project monitors
   - What scoring or status system is used (tiers, levels, scores, etc.)
   - What the key entities and data types are
2. Identify the relevant API endpoints by reading `backend/app/routers/` or the API spec in the PRD

## Step 2: Verify Backend is Running

Run a quick health check:

```bash
curl -sf http://localhost:8000/api/health
```

If the backend is not reachable, stop and tell the user:
> "Backend is not running at localhost:8000. Start it with: `cd backend && uv run uvicorn app.main:app --reload --port 8000`"

## Step 3: Gather Data

Fetch all available data sources in parallel using curl. Adapt endpoints to match what the project actually exposes.

Common patterns to look for:
1. **Scores / Status** — composite scores, module statuses, decision matrices
2. **High-Severity Items** — alerts, signals, or events above a severity threshold
3. **Recent Activity** — events, logs, or data points from the last 7 days
4. **Active Alerts** — any triggered alerts or threshold breaches
5. **Snapshots / Exports** — summary or export endpoints

If any endpoint returns an error or 404, note it but continue with available data. Not all endpoints may be implemented yet.

## Step 4: Analyze and Compose Briefing

Using the fetched data, compose a structured briefing. Adapt the sections below to fit the project's domain:

### Executive Summary
2-3 sentences summarizing the current overall status. Reference the highest severity level and any top-level recommendation or decision output.

### Status by Module/Domain
- List each module or monitored domain
- For each: current score/level, label, and trend direction (rising/stable/falling)
- State any composite or decision matrix output

### Key Developments
- Summarize the most significant events or signals from the last 7 days
- Group by module/domain where helpful
- Highlight anything at the highest severity levels

### Trend Assessment
- Note which indicators are trending up vs down
- Flag any rapid changes or threshold crossings
- Mention any domain scores that shifted significantly

### Active Alerts
- Note any active alerts or watchlist matches
- Flag patterns worth monitoring

### Recommended Actions
- Based on current levels and any decision logic, state recommended posture
- If any module is at elevated levels, call out specific attention areas

Format the briefing in clean markdown suitable for reading.

## Step 5: Post Briefing to API (if supported)

Unless `$ARGUMENTS` contains "dry-run", attempt to POST the briefing to the API:

```bash
curl -s -X POST http://localhost:8000/api/briefings \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Situation Briefing — YYYY-MM-DD",
    "briefing_type": "situation",
    "content": "... full briefing markdown ...",
    "summary": "... executive summary text ...",
    "generated_by": "claude-code"
  }'
```

- Use today's date in the title
- Include any additional fields the project's briefing schema expects (check schemas/)
- If the POST returns 404 or 422 (endpoint not yet implemented), note it and still print the briefing

The briefing content is the primary output regardless of whether the POST succeeds.

## Step 6: Report Result

Print the full briefing to the user, then:

- If POST succeeded: "Briefing saved with ID: [id from response]"
- If POST failed or was skipped: "Briefing generated (not saved — endpoint returned [status] or dry-run mode)"
- Print a one-line status summary appropriate to the project domain

## Notes

- All API requests go to `http://localhost:8000` by default
- Use `curl -s` (silent) for clean output
- If data is sparse (e.g., no signals yet), still generate the briefing with available data and note gaps
- The briefing should read like a professional summary — concise, factual, actionable
- Do not fabricate data — if an endpoint returns empty results, say "No data available" for that section
- Adapt section names and terminology to match the project's domain (not every project is geopolitical monitoring)
