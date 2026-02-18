# AI Design Workflow: Building Taste

> Source: [@Av1dlive](https://x.com/Av1dlive/status/2023086925817729306)

The bottleneck in AI-assisted design isn't better prompts — it's developing **taste**. AI can execute anything; your job is knowing what's worth executing.

---

## Core Philosophy

**Build Taste > Generate > Iterate**

Treat AI like a junior designer, not a black box. Guide it through the same progression a professional follows: low-fidelity wireframes > high-fidelity mockups > polished micro-interactions.

---

## The 5-Step Workflow

### Step 1: Build Taste Through Visual References

Before touching any AI tool, spend 20-30 minutes gathering screenshots of interfaces that **solve the same problem you're working on** (not just "pretty" designs).

**Where to look:**
- [Awwwards](https://www.awwwards.com/) — cutting-edge web design
- [Pinterest AI search](https://pinterest.com/) — upload a screenshot, find similar designs
- [Dribbble](https://dribbble.com/) — UI patterns

**Pro tip:** Don't just save images. Ask yourself: *"What specifically works here?"* Is it the hierarchy? The spacing? The color restraint? Naming it builds your design vocabulary.

---

### Step 2: Video-to-Code (Reverse Engineering)

Record a screen capture of any website you like and have AI recreate the code. Tools like Kimi K2.5 can parse structure, components, and transitions frame-by-frame.

**Prompt for reverse-engineering screen recordings:**

```
Analyze this website video and recreate the HTML/CSS/JS with the following focus:

LAYOUT & STRUCTURE:
- Maintain the exact visual hierarchy (header, sections, footer)
- Preserve spacing relationships between elements (margins, padding, gaps)
- Replicate the grid system and responsive behavior

TYPOGRAPHY:
- Match font sizes, weights, and line heights
- Preserve text hierarchy (H1, H2, body, captions)
- Maintain letter spacing and text alignment

COMPONENTS:
- Recreate navigation structure and behavior
- Build cards, buttons, forms with matching styles
- Include hover states and interactive elements

ANIMATIONS & INTERACTIONS:
- Capture scroll-triggered animations and their timing
- Replicate transition speeds and easing functions
- Include sticky elements, parallax, or fade effects

OUTPUT:
- Clean, semantic HTML structure
- Organized CSS with clear class naming
- Functional JavaScript for interactions
- Component-based architecture where possible
```

This captures ~80% of the aesthetic immediately.

---

### Step 3: Learn the Language of Design

**You can't prompt what you can't name.** Precision beats vague requests like "make it prettier."

#### Typography
| Term | Definition |
|------|-----------|
| Hierarchy | H1 > H2 > H3 > body > captions |
| Kerning | Space between two letters |
| Leading | Vertical line spacing |
| Weight | thin / regular / bold / black |

#### Layout
| Term | Definition |
|------|-----------|
| White space | Breathing room around elements |
| Proximity | Grouping related items together |
| Contrast ratio | 4.5:1 minimum for accessibility |

#### Color System
| Role | Usage |
|------|-------|
| Primary | Brand colors (max 2) |
| Accent | CTAs and highlights |
| Semantic | Error red, success green, warning yellow |
| Neutral | Text grays |

**Why this matters:** "Reduce the leading on H2s to 1.4" gets perfect results. "Make it look better" doesn't.

---

### Step 4: Meta-Prompts + Skills

**Meta-prompts create range. Skills lock in quality.** Use both.

#### Phase 1: Meta-Prompts

Use AI to write design prompts for AI. Feed your visual references and ask it to generate a "vibe spec" (style intent, hierarchy, interaction feel). Then use that spec when generating code.

```
Generate a ONE-PAGE LANDING PAGE prompt using a RANDOMLY SELECTED design style:
Neobrutalist, Swiss, Editorial, Glassmorphism, Bauhaus, Minimal, Japandi,
Dark Mode First, Gradient Modern, Typography First.

Output THREE PARAGRAPHS:
1) Emotional qualities + visitor mood + visual hierarchy feel
2) Typography feel + interaction sensations + emotional journey
3) Abstract references (spaces, movements, periods) — NO brand names

Focus on FEELING and ATMOSPHERE. Avoid implementation details.
```

- Look at the original prompt by user **JCodesMore** on r/PromptEngineering for a starting point
- Volume builds taste — produce dozens of distinct vibe specs quickly

#### Phase 2: Skills (Reusable Rule Packs)

Skills are reusable, task-scoped rule packs (constraints + checks + workflows) that agents apply whenever the task matches.

**Recommended skills:**

| Skill | Purpose | Install |
|-------|---------|---------|
| **UI Skills** (by @Ibelick) | Prevent common mistakes — accessibility, motion, metadata | `npx ui-skills init` |
| **UI/UX Pro Max** | Searchable design playbook for web + mobile | — |
| **RAMS** (by @elirousso) | Final QA with line-level fixes before shipping | — |

#### Recommended workflow with skills:
1. Meta-prompt > vibe spec (style intent, hierarchy, interaction feel)
2. Generate code
3. Apply skills (baseline constraints: UI + motion + metadata)
4. Run UI/UX Pro Max review (standardize production UI patterns)
5. Final polish with RAMS (fix flagged issues, ship)

Once constraints live as skills, every future UI inherits them — less rework, fewer regressions, more consistent taste.

---

### Step 5: The Zoom-In Method (50% > 99% > 100%)

Stop expecting AI to nail it in one shot. Use three progressive passes:

#### Pass 1 — Full Context Dump (50%)

Give AI everything upfront:

```
Create a [product type] for [target users].

Core features/pages: [list all pages/sections]

Color palette: [specific hex codes]
Typography: [font choices if any]
Style: [mood/vibe descriptors]

Key components needed: [list all UI elements]

Technical constraints: [framework, accessibility needs, etc.]

Reference structure: [paste example code or link]
```

#### Pass 2 — Self-Review (99%)

Prompt the AI to review its own work. It catches ~70% of its own mistakes — font sizes, padding inconsistencies, hierarchy issues — without you pointing them out.

Repeat for each page.

#### Pass 3 — Micro Polish (100%)

Target specific elements with precise instructions. Being specific = better results. Screenshots help even more.

```
On [specific component/page], make these precise changes:

1. [Specific spacing adjustment with px values]
2. [Exact color/state change with hex codes]
3. [Precise alignment fix with measurements]
4. [Animation timing with ms/easing function]
5. [Any other micro-adjustments]
```

**Example:** Instead of "fix the header," say "reduce header padding from 24px to 16px on mobile, increase logo size to 32px, and align nav items to baseline."

---

## Quick Reference

| Old Way | New Way |
|---------|---------|
| 6-8 hours manual labor | 1-2 hours strategic guidance |
| "Make it look good" | Professional terminology with exact values |
| Random generation | Curated references killing generic outputs |
| One-shot prompting | Zoom-In Method (50% > 99% > 100%) |

**The new design moat:** Your taste and process are your competitive advantages. Precision, curation, and the Zoom-In Method handle the heavy lifting while you focus on cohesion and empathy.
