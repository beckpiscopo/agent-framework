# Agent Framework Template

A template repository implementing the "Top 1% Agentic Engineer" workflow for Claude Code.

## What's Included

```
.
├── .claude/
│   ├── commands/               # Slash commands for Claude Code
│   │   ├── commit.md           # /commit - smart commit workflow
│   │   └── review.md           # /review - code review
│   ├── reference/              # Best practices docs (permanent)
│   │   ├── api.md              # FastAPI patterns
│   │   ├── components.md       # React/Next.js patterns
│   │   ├── deploy.md           # Deployment patterns
│   │   └── testing.md          # Testing patterns
│   └── PRD.md                  # Product requirements template
│
├── .agents/
│   └── plans/                  # Implementation plans (per feature)
│       └── README.md           # How to write plans
│
├── CLAUDE.md                   # Main documentation (Claude reads this first)
├── WORKFLOW.md                 # Context reset workflow guide
└── README.md                   # This file
```

## Quick Start

### 1. Use This Template

Click "Use this template" on GitHub, or:

```bash
git clone https://github.com/YOUR_USERNAME/agent-framework.git my-project
cd my-project
rm -rf .git
git init
```

### 2. Customize for Your Project

1. Update `CLAUDE.md` with your project name and description
2. Edit `.claude/PRD.md` with your requirements and feature phases
3. Adjust `.claude/reference/` docs for your specific stack

### 3. Start Building

Open Claude Code and start with:

```
Read CLAUDE.md and .claude/PRD.md. Then let's begin with Phase 1.
```

## The 5 Meta-Skills

This template implements:

### 1. PRD-First Development
`.claude/PRD.md` is your source of truth. Every AI conversation references it.

### 2. Modular Rules Architecture
`.claude/reference/` contains split-by-concern documentation. Load only what's relevant:
- Working on API? → Read `.claude/reference/api.md`
- Working on frontend? → Read `.claude/reference/components.md`

### 3. Command-ify Everything
Repetitive workflows become `/commands`:
- `/commit` - Smart conventional commits
- `/review` - Code quality review

Add your own in `.claude/commands/`.

### 4. The Context Reset
See `WORKFLOW.md`. Key insight: **Plan in one conversation, execute in a fresh one.**

Plans go in `.agents/plans/` - one file per feature.

### 5. System Evolution
Every bug is an opportunity:
- Wrong patterns? → Add to `.claude/reference/` docs
- Missing rules? → Update `CLAUDE.md`
- Repetitive task? → Create a new command

## Customization

### Adding Commands

Create `.claude/commands/your-command.md`:

```markdown
# Command Name

Description of what this command does.

## Steps

1. First step
2. Second step
3. ...
```

Use with: `/your-command`

### Adding Reference Docs

Create `.claude/reference/your-domain.md` and add it to the table in `CLAUDE.md`.

### Creating Implementation Plans

During the PLAN phase, create `.agents/plans/feature-name.md` with:
- Overview of what you're building
- Files to create/modify
- Step-by-step implementation guide
- Validation steps

Then reset context and execute from the plan.

### Modifying for Your Stack

The included reference docs assume FastAPI + Next.js + Supabase. Adapt for your stack:
- Django? Replace `api.md` patterns
- Vue? Replace `components.md` patterns
- Different DB? Update `deploy.md` accordingly

## Workflow Cheatsheet

| Phase | What to Do |
|-------|------------|
| **PLAN** | Research in one conversation, create `.agents/plans/feature.md` |
| **RESET** | Close conversation |
| **EXEC** | Fresh conversation: "Read `.agents/plans/feature.md` and implement it" |

## Credits

This framework is based on the "Top 1% Agentic Engineer" methodology by [Cole Medin](https://github.com/coleam00).

Original infographic: [Top1%AgenticEngineering.png](https://github.com/coleam00/habit-tracker/blob/main/Top1%25AgenticEngineering.png)

## License

MIT - Use freely, modify as needed.
