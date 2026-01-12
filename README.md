# Agent Framework Template

A template repository implementing the "Top 1% Agentic Engineer" workflow for Claude Code.

## What's Included

```
.
├── .claude/commands/           # Slash commands for Claude Code
│   ├── commit.md               # /commit - smart commit workflow
│   └── review.md               # /review - code review
│
├── .agents/                    # Modular rules (on-demand context)
│   ├── AGENTS.md               # Main context loader
│   └── reference/
│       ├── api.md              # FastAPI patterns
│       ├── components.md       # React/Next.js patterns
│       ├── deploy.md           # Deployment patterns
│       └── testing.md          # Testing patterns
│
├── CLAUDE.md                   # Global rules for Claude Code
├── PRD.md                      # Product requirements template
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

### 2. Customize PRD.md

Open `PRD.md` and replace the example content with your project's:
- Project name and description
- Tech stack specifics
- Feature phases and requirements

### 3. Start Building

Open Claude Code and start with:

```
Read PRD.md and CLAUDE.md. Then let's begin with Phase 1.
```

## The 5 Meta-Skills

This template implements:

### 1. PRD-First Development
`PRD.md` is your source of truth. Every AI conversation references it.

### 2. Modular Rules Architecture
`.agents/` contains split-by-concern documentation. Load only what's relevant:
- Working on API? → Read `.agents/reference/api.md`
- Working on frontend? → Read `.agents/reference/components.md`

### 3. Command-ify Everything
Repetitive workflows become `/commands`:
- `/commit` - Smart conventional commits
- `/review` - Code quality review

Add your own in `.claude/commands/`.

### 4. The Context Reset
See `WORKFLOW.md`. Key insight: **Plan in one conversation, execute in a fresh one.**

### 5. System Evolution
Every bug is an opportunity:
- Wrong patterns? → Add to reference docs
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

Create `.agents/reference/your-domain.md` and link it in `.agents/AGENTS.md`.

### Modifying for Your Stack

The included reference docs assume FastAPI + Next.js. Adapt them for your stack:
- Django? Replace `api.md` patterns
- Vue? Replace `components.md` patterns
- Different DB? Update `api.md` accordingly

## Workflow Cheatsheet

| Phase | What to Do |
|-------|------------|
| **PLAN** | Research in one conversation, create implementation plan doc |
| **DOC** | Capture everything in `docs/plans/feature-name.md` |
| **RESET** | Close conversation |
| **EXEC** | Fresh conversation: "Read the plan and implement it" |

## License

MIT - Use freely, modify as needed.
