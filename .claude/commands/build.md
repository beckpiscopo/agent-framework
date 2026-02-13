# Build Next Step

Read the current implementation plan and execute the next incomplete step.

## Arguments

$ARGUMENTS - Optional: specific step number to execute (e.g., "step 5"), or "continue" to resume from last incomplete step (default: continue)

## Process

### Step 1: Load Plan & Determine Current Step

1. Read `.claude/PRD.md` for project context
2. Find the active implementation plan:
   - List files in `.agents/plans/`
   - If multiple plans exist, pick the one matching the current phase (check file names)
   - If `$ARGUMENTS` specifies a plan name, use that
3. Parse the plan to find:
   - All steps and their checkbox status
   - The first step with unchecked `- [ ]` tasks (this is the "next step")
   - If `$ARGUMENTS` specifies a step number, use that instead
4. Report: "**Current progress:** Steps 1-N complete. Starting Step N+1: [step name]"

### Step 2: Check Prerequisites

Before building, verify:
- [ ] Are the step's dependencies complete? (check that prior steps are checked off)
- [ ] Are required services running? (Docker, backend, etc. — based on what the step needs)
- [ ] If a prerequisite is missing, either fix it or report what needs to be done first

If prerequisites aren't met:
> "Step N requires [prerequisite] which isn't ready. Should I fix that first, or skip to a step I can work on?"

### Step 3: Execute the Step

1. Read the step's **Goal**, **Tasks**, and **Validation** sections carefully
2. Implement each task in order:
   - Create/modify files as specified
   - Follow project conventions from `.claude/reference/` docs
   - Follow patterns established in earlier steps (read existing code first)
3. After each file is created/modified, verify it's syntactically correct
4. Run any tests mentioned in the step's tasks
5. For steps that involve multiple independent files, use parallel agents where possible

### Step 4: Validate

1. Execute the **Validation** commands listed in the step
2. If validation fails:
   - Diagnose the issue
   - Fix it
   - Re-run validation
   - If stuck after 3 attempts, report the issue and ask for guidance
3. If validation passes, proceed to mark complete

### Step 5: Mark Complete & Commit

1. Update the plan file: change `- [ ]` to `- [x]` for all completed tasks in this step
2. Run `/commit` to commit the work with a message like:
   ```
   feat(phase-1): complete step N — [step name]
   ```
3. Report results:
   > "**Step N complete:** [summary of what was built]
   > **Validation:** [pass/fail details]
   > **Next up:** Step N+1 — [next step name]
   >
   > Run `/build` again to continue, or `/validate` to re-check."

### Step 6: Continue or Stop

- If the user said "continue" or didn't specify, ask:
  > "Step N is done. Want me to continue to Step N+1, or stop here?"
- If the user originally said a specific step number, stop after that step

## Error Recovery

If something goes wrong mid-step:
1. Do NOT leave the codebase in a broken state
2. Either fix the issue or revert the changes for the current step
3. Report exactly what happened and what's still needed
4. Update the plan with notes about blockers if relevant

## Notes

- Always read existing code before creating new code that depends on it
- Follow established patterns — if Step 3 created models a certain way, Step 4 should match
- Run tests after each step, not just at the end
- Prefer small, validated increments over large untested changes
- If a step is too large (>30 files), break it into sub-steps and execute sequentially
