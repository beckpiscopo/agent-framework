# Design System Generator

Interactive command to establish or update the project's design system.

## When to Use

- Starting a new project with UI
- Resetting/updating design direction mid-project
- Onboarding a new developer to understand design tokens

## Workflow

### If `.design-system/system.md` exists:

1. Read and summarize the current design system
2. Display: direction, colors, fonts, spacing, depth strategy
3. Ask: "Want to modify anything, or is this just for reference?"
4. If modifying, walk through relevant sections

### If no design system exists:

**Step 1: Project Context**
Ask:
- What type of app is this? (dashboard, consumer app, marketing site, etc.)
- Who are the target users?
- Any existing brand colors or fonts to incorporate?

**Step 2: Design Direction**
Present options and ask user to pick one:

| Direction | Feel | Best For |
|-----------|------|----------|
| **Precision & Density** | Tight, technical, monochrome | Developer tools, admin dashboards, data-heavy apps |
| **Sophistication & Trust** | Cool tones, refined elegance | Finance, enterprise B2B, banking apps |
| **Warmth & Approachability** | Generous spacing, soft shadows | Consumer apps, collaborative tools |
| **Boldness & Clarity** | High contrast, dramatic space | Marketing sites, modern dashboards |
| **Editorial & Magazine** | Typography-first, white space | Content-heavy, reading experiences |
| **Brutalist & Raw** | Exposed structure, honest | Developer portfolios, experimental |

**Step 3: Colors**
Based on direction, suggest a palette:
- Primary (text, strong elements)
- Accent (buttons, links, highlights)
- Background
- Muted (secondary text)
- Border

Allow user to customize each with hex values.

**Step 4: Typography**
Suggest font combinations based on direction:
- Display font (headings)
- Body font (paragraphs, UI)
- Mono font (numbers, code)

Recommend Google Fonts. Allow customization.

**Step 5: Spacing & Radius**
- Base unit: 4px (dense) or 8px (generous)
- Border radius: None, Small (2/4/6), Medium (4/8/12), Large (8/12/16)

**Step 6: Depth Strategy**
- Flat (no shadows)
- Borders only (1px borders define hierarchy)
- Subtle shadows
- Layered shadows
- Hard shadows (dramatic offset)

**Step 7: Generate**
Create `.design-system/system.md` with:
- CSS custom properties
- Tailwind config snippet
- Component patterns (button, card, input)
- AI prompting reference block

## Output

Create the `.design-system/` directory if it doesn't exist, then save to `.design-system/system.md`.

The generated file should include:
- Direction name and description
- CSS custom properties (light + dark mode if requested)
- Google Fonts import snippet
- Spacing scale based on chosen base unit
- Border radius values
- Depth strategy CSS
- Component patterns (button, card, input)
- Tailwind config snippet
- AI prompting reference block

Confirm: "Design system saved to `.design-system/system.md`! It will auto-load for all future UI work in this project."

## Example Interaction
```
User: /design-system

Claude: I don't see an existing design system. Let's create one!

What type of app are you building?

User: A personal finance tracker, like YNAB

Claude: Great! For a finance app, I'd suggest **Sophistication & Trust** - 
it conveys competence and reliability. Does that fit, or would you prefer 
a different direction?

User: That works. But I want a darker theme.

Claude: Got it. Here's my suggested palette for a dark sophisticated finance app:

- Primary: #f8fafc (light text)
- Accent: #0d9488 (teal for positive actions)
- Background: #0f172a (deep navy)
- Muted: #94a3b8 (secondary text)
- Border: #334155 (subtle dividers)

Want to adjust any of these?

...continues through fonts, spacing, depth...

Claude: ✓ Design system saved to `.design-system/system.md`

It includes:
- CSS variables for light/dark mode
- Tailwind config
- Button, Card, Input patterns
- AI prompting reference

This will auto-load whenever you work on UI. Run `/design-system` again 
anytime to view or modify it.
```