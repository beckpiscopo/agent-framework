# Frontend Design Reference

Permanent reference for design principles. Load `.design-system/system.md` for project-specific tokens.

## Core Principle: Avoid AI Slop

AI-generated UIs converge to generic aesthetics without guidance:
- Inter/Roboto/Arial fonts
- Purple gradients on white backgrounds
- Predictable card grids
- Generic drop shadows everywhere

**Always** check for `.design-system/system.md` before UI work. If it doesn't exist, run `/design-system`.

---

## Design Directions Quick Reference

| Direction | Colors | Depth | Typography | Best For |
|-----------|--------|-------|------------|----------|
| Precision & Density | Cool grays, blue accent | Borders | Space Grotesk / Inter | Dashboards, dev tools |
| Sophistication & Trust | Navy, teal, warm white | Subtle borders | Plus Jakarta Sans | Finance, enterprise |
| Warmth & Approachability | Warm neutrals, orange | Soft shadows | DM Sans | Consumer, social |
| Boldness & Clarity | Black/white, neon accent | Hard shadows | Bricolage Grotesque | Marketing, modern |
| Editorial & Magazine | Minimal, 2-3 colors | Flat | Playfair + Source Serif | Content, blogs |
| Brutalist & Raw | System colors | Flat | Space Mono | Experimental |

---

## Typography Rules

### Never Use (Generic)
- Inter, Roboto, Arial, Open Sans, Lato
- System fonts for display text

### Good Combinations

**Technical/Dashboard:**
```
Display: Space Grotesk (600, 700)
Body: Inter (400, 500)  
Mono: JetBrains Mono
```

**Sophisticated/Finance:**
```
Display: Plus Jakarta Sans (600, 700)
Body: Plus Jakarta Sans (400, 500)
Mono: JetBrains Mono
```

**Editorial/Content:**
```
Display: Playfair Display (600, 700)
Body: Source Serif 4 (400, 500)
Mono: IBM Plex Mono
```

**Friendly/Consumer:**
```
Display: DM Sans (600, 700)
Body: DM Sans (400, 500)
Mono: DM Mono
```

### Weight Rules
- Use extremes: 200 vs 800, not 400 vs 500
- Size jumps of 1.5x-2x minimum between hierarchy levels

### Numbers
Always use `font-variant-numeric: tabular-nums` for:
- Financial data
- Tables with numbers
- Timestamps
- Any aligned numerical data

---

## Color Rules

### Palette Size
- 5 colors max for UI
- Primary, Accent, Background, Muted, Border
- Add semantic colors: Success, Warning, Error

### Relationships
```css
/* Light mode */
--background: light
--foreground: dark (primary)
--muted: medium gray
--border: light gray

/* Dark mode - invert relationships */
--background: dark (use former primary)
--foreground: light (use former background)
--muted: medium (adjust for contrast)
--border: darker than muted
```

### Accent Color
- One strong accent for CTAs and highlights
- Don't distribute colors evenly
- Dominant neutral + punchy accent > balanced rainbow

---

## Spacing System

### Base Unit
- **4px** for dense UIs (dashboards, data tables)
- **8px** for generous UIs (marketing, consumer)

### Scale
```
1x:  4px  (or 8px)
2x:  8px  (or 16px)
3x:  12px (or 24px)
4x:  16px (or 32px)
6x:  24px (or 48px)
8x:  32px (or 64px)
12x: 48px (or 96px)
16x: 64px (or 128px)
```

### Rule
**Never use arbitrary values** like 17px, 22px, 37px. Everything on the grid.

---

## Depth Strategies

Pick ONE and stick to it:

| Strategy | CSS | Feel |
|----------|-----|------|
| **Flat** | No shadows, no borders | Raw, brutalist |
| **Borders** | `1px solid var(--border)` | Technical, precise |
| **Subtle Shadows** | `0 1px 3px rgba(0,0,0,0.1)` | Professional, clean |
| **Layered Shadows** | Multiple layers | Warm, elevated |
| **Hard Shadows** | `4px 4px 0 var(--primary)` | Bold, dramatic |

### Don't Mix
If you're using borders for depth, don't add shadows to some cards. Consistency > variety.

---

## Border Radius

| Style | Values | Feel |
|-------|--------|------|
| None | 0 everywhere | Brutalist, technical |
| Small | 2/4/6px | Precise, professional |
| Medium | 4/8/12px | Balanced, modern |
| Large | 8/12/16px | Friendly, soft |

### Nested Radius Rule
Child radius ≤ parent radius. If card is 12px, inner elements are 8px or less.

---

## Component States

### Interactive Elements (Buttons, Links, Inputs)

Every one needs:
- Default
- Hover (`:hover`)
- Focus (`:focus-visible` with visible ring)
- Active (`:active`)
- Disabled (reduced opacity, `cursor: not-allowed`)
- Loading (if async action)

### Data Displays (Cards, Lists, Tables)

Every one needs:
- Empty state (no data yet)
- Loading state (skeleton or spinner)
- Error state (with retry action)
- Populated state

---

## Accessibility Checklist

From Vercel Web Interface Guidelines:

### Keyboard
- [ ] All interactions keyboard-operable
- [ ] Logical tab order
- [ ] Focus visible on `:focus-visible`
- [ ] No `outline: none` without replacement
- [ ] Enter submits forms

### Touch
- [ ] Touch targets ≥44px on mobile
- [ ] No double-tap zoom on controls (`touch-action: manipulation`)

### Visual
- [ ] Color contrast ≥4.5:1 (prefer APCA over WCAG 2)
- [ ] Don't rely on color alone (add icons/text)
- [ ] `prefers-reduced-motion` respected

### Semantic
- [ ] Proper heading hierarchy (h1 → h2 → h3)
- [ ] Labels on all form inputs
- [ ] `aria-label` on icon-only buttons
- [ ] Native elements before ARIA (`<button>` not `<div role="button">`)

---

## Before Any UI Task

```
1. Does `.design-system/system.md` exist?
   ├─ Yes → Load it, follow its tokens
   └─ No → Run `/design-system` first

2. Reference this doc for principles

3. After writing UI code, validate:
   - Typography matches system
   - Colors use variables
   - Spacing on grid
   - Depth strategy consistent
   - All states designed
   - Accessibility covered
```

---

## Resources

- [Vercel Web Interface Guidelines](https://vercel.com/design/guidelines)
- [APCA Contrast Checker](https://apcacontrast.com/)
- [Google Fonts](https://fonts.google.com/)
- [Tailwind CSS](https://tailwindcss.com/docs)
