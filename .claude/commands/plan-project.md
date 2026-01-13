# Plan New Project

Help me plan a new project from a rough idea and generate a complete PRD.

## Arguments

$ARGUMENTS - Optional: The rough idea to start with (can also be provided interactively)

## Process

### Step 1: Capture the Idea

If no idea was provided in $ARGUMENTS, ask:
> "What's your project idea? Describe it in 2-3 sentences - what does it do and who is it for?"

### Step 2: Discovery Questions

Ask these questions ONE AT A TIME (wait for each answer before asking the next):

1. **Problem & Users**
   > "What specific problem does this solve? Who experiences this problem most acutely?"

2. **Existing Solutions**
   > "How do people currently solve this problem? What's frustrating about those solutions?"

3. **Core Value**
   > "If this project could only do ONE thing really well, what would it be?"

4. **Success Criteria**
   > "How will you know if this project is successful? What does 'done' look like for v1?"

5. **Constraints**
   > "Any technical constraints, timeline pressures, or must-have integrations I should know about?"

### Step 3: Synthesize & Propose

Based on the answers, propose:

1. **One-liner**: A single sentence describing the project
2. **Core features** (3-5 for MVP)
3. **Out of scope** (things to explicitly NOT build in v1)
4. **Suggested phases**:
   - Phase 1: Foundation (MVP)
   - Phase 2: Core Features
   - Phase 3: Polish & Scale

Present this summary and ask:
> "Does this capture your vision? What would you add, remove, or change?"

### Step 4: Tech Stack Confirmation

Propose a tech stack based on the project needs and ask for confirmation:
> "For this project, I'd suggest: [stack]. Does this work for you, or do you have preferences?"

Default suggestion (adjust based on project):
- **Backend**: FastAPI + Supabase
- **Frontend**: Next.js + Tailwind + React Query
- **Infrastructure**: Vercel + Supabase

### Step 5: Generate PRD

Once approved, generate the complete PRD:

1. Read the PRD template at `.claude/PRD.md` for structure reference
2. Create a NEW file at `.claude/PRD.md` (overwriting the template) with:
   - Project name and description
   - Problem statement
   - Complete tech stack
   - Feature phases with granular requirements
   - Success criteria for each phase
   - API endpoints (draft)
   - Data models (draft)

### Step 6: Asana Integration (Optional)

After generating the PRD, ask:
> "Would you like me to create Asana tasks from this PRD? I can set up a project with tasks for each phase."

If the user says yes:

1. **Get Workspace**: Use `asana_list_workspaces` to find available workspaces. If multiple exist, ask which one to use.

2. **Create Project**: Use `asana_create_project` to create a new project:
   - Name: `[Project Name]` (from PRD)
   - Default view: `board` (for Kanban-style workflow)
   - Set appropriate team if in an organization

3. **Get Sections**: Use `asana_get_project_sections` to get the default sections, then decide if you need custom sections for each phase.

4. **Create Tasks**: For each phase in the PRD, use `asana_create_task` to create:
   - **Phase milestone tasks** (use `resource_subtype: "milestone"`) for phase completion markers
   - **Feature tasks** for each major feature (1.1, 1.2, etc.)
   - **Subtasks** for individual requirements within each feature (use `parent` parameter)

   Task structure example:
   ```
   📌 Phase 1: Foundation (Milestone)
      └── 1.1 User Authentication
          ├── User registration with email/password
          ├── Login/logout functionality
          └── JWT token management
      └── 1.2 Core Data Model
          ├── Define database schema
          └── Create SQLAlchemy models
   ```

5. **Link Tasks**: Use `asana_set_task_dependencies` to create dependencies between phases (Phase 2 depends on Phase 1 milestone, etc.)

6. **Report Back**: Provide the Asana project URL and summary:
   > "Created Asana project '[Project Name]' with X tasks across Y phases.
   > View it here: [Asana URL]"

### Step 7: Confirm & Next Steps

After generating the PRD (and optionally Asana tasks), summarize:
> "PRD created at `.claude/PRD.md`. Here's what I recommend next:
> 1. Review the PRD and refine any details
> 2. Create an implementation plan for Phase 1: `.agents/plans/phase-1-foundation.md`
> 3. Start building!
>
> Want me to create the Phase 1 implementation plan now?"

## Tips for Good PRDs

- Features should be specific and testable
- Each phase should be independently deployable
- MVP should be achievable in 1-2 weeks of focused work
- Success criteria should be measurable

## Asana Integration Notes

### Required MCP Tools
The Asana integration uses these MCP tools:
- `asana_list_workspaces` - Discover available workspaces
- `asana_typeahead_search` - Find existing projects/teams
- `asana_create_project` - Create the project container
- `asana_get_project_sections` - Get/manage sections
- `asana_create_task` - Create tasks and subtasks
- `asana_set_task_dependencies` - Link phase dependencies

### Task Naming Convention
- Milestones: `Phase X: [Phase Name]`
- Features: `X.Y [Feature Name]` (e.g., "1.1 User Authentication")
- Subtasks: Direct requirement text from PRD

### Custom Fields (Optional)
If the workspace has custom fields for effort estimation or priority, you can set them using the `custom_fields` parameter as JSON:
```json
{"field_gid": "value"}
```

### Handling Large PRDs
For PRDs with many requirements, batch task creation to avoid rate limits. Create phase milestones first, then features, then subtasks in sequence.
