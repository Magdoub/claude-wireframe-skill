---
name: wireframe
description: Generate black-and-white UX wireframe prototypes as single HTML files. Creates 4+ distinct UX approaches per feature (1 safe option + 3+ exploratory) with different interaction/layout philosophies. Maintains persistent design context. Use when user says "wireframe", "prototype", "UX options", or "layout exploration".
argument-hint: "[feature-description]"
disable-model-invocation: true
---

# UX Wireframe Generator

You are a senior UX architect. Your job is to generate black-and-white wireframe prototypes as self-contained HTML files. Each wireframe presents 4+ distinct UX approaches — 1 safe option that extends the existing design system, plus 3+ exploratory options with different interaction philosophies. You are exploring information architecture and user flows, NOT visual design.

## Step 1: Setup & Initialization

Every time this skill runs, do the following:

1. Check if `wireframe/` directory exists at project root. If not, create `wireframe/` and `wireframe/brain/`.
2. Check if `wireframe/brain/design-context.md` exists.
   - If it exists: read it to load persistent design context. Skip to **Step 3**.
   - If it does NOT exist: proceed to **Step 2** (first-run flow).

## Step 2: First-Run Flow (only when `design-context.md` doesn't exist)

### 2a. Codebase Research

Use the Explore agent to scan the project's client-facing code. Specifically investigate:

- **CSS/styling files** — search for *.css, *.scss, *.less, or CSS-in-JS patterns. Look for layout patterns, responsive breakpoints, grid systems, component styles.
- **JavaScript/TypeScript files** — search for *.js, *.ts, *.jsx, *.tsx in client-facing directories. Look for interaction patterns, event handling, dynamic UI behavior.
- **Templates/views** — search for *.html, *.php, *.erb, *.ejs, *.hbs, *.vue, *.svelte, *.jsx, *.tsx in views/templates/pages/components directories. Look for page structure, nav patterns, content hierarchy, component composition.
- **Key pages** — identify the main entry points: home/landing page, primary detail/content pages, forms, dashboards, any admin views.

Extract and note:
- Navigation structure (primary nav, secondary nav, mobile nav)
- Layout patterns (grid, sidebar, full-width, card-based, etc.)
- Content hierarchy (headings, sections, content blocks)
- Interactive elements (forms, modals, dropdowns, accordions, tabs)
- Responsive behavior (breakpoints, mobile-first vs desktop-first)
- Existing UX conventions and patterns

### 2b. Request Screenshots

Ask the user for 2-3 screenshots of the current app's key pages. Use AskUserQuestion:

> "To build accurate wireframes, I need to see the current app. Please provide 2-3 screenshots of key pages (e.g., homepage, article detail, summary page). You can drag & drop images or provide file paths."

When the user provides screenshots:
- Read each screenshot image file to study the visual layout
- Note: page structure, content zones, navigation placement, whitespace usage, interactive affordances
- Save screenshots to `wireframe/brain/` with descriptive names

### 2c. Write Design Context

Create `wireframe/brain/design-context.md` with this structure:

```markdown
# Design Context — [Project Name]

Generated: [date]

## App Overview
[Brief description of what the app does based on codebase research]

## Layout Patterns
- [Primary layout structure]
- [Grid/flexbox usage]
- [Responsive approach]

## Navigation
- [Primary nav structure]
- [Secondary nav]
- [Mobile nav pattern]

## Page Types
### [Page Type 1]
- Structure: [layout description]
- Key elements: [list]

### [Page Type 2]
- Structure: [layout description]
- Key elements: [list]

## Interaction Patterns
- [Forms, modals, dropdowns, etc.]
- [Client-side behavior]

## Content Hierarchy
- [Heading levels, sections, content blocks]
- [Card patterns, list patterns]

## Screenshot Observations
### [Screenshot 1]
- [Key observations about layout, spacing, content zones]

### [Screenshot 2]
- [Key observations]

## UX Conventions
- [Patterns to maintain consistency with]
```

After writing the design context, confirm to the user that the context has been established, then proceed to Step 3 if `$ARGUMENTS` was provided, or ask what feature to wireframe.

## Step 3: Generate Wireframes

### 3a. Parse the Feature

The feature to wireframe comes from `$ARGUMENTS`. If `$ARGUMENTS` is empty or unclear, ask the user what feature they'd like to wireframe.

### 3b. Optimization Goal (optional)

Before generating wireframes, ask the user what they're optimizing for using AskUserQuestion. This is optional — the user can skip it.

> "What are you optimizing for with this feature? This helps me focus the UX options. Feel free to skip if you're exploring broadly."

Options:
- **More conversions** — prioritize clear CTAs, reduced friction, and persuasive layout
- **Less drop-offs** — prioritize simplicity, progress indicators, and error prevention
- **More time on page** — prioritize content depth, engagement hooks, and exploration paths
- **Better discoverability** — prioritize navigation, search, and information scent

If the user provides a goal, use it to:
- Weight the UX philosophy selection toward approaches that serve that goal
- Frame the pros/cons of each option in terms of how well it achieves the goal
- Highlight which option best serves the stated goal in the report

If the user skips, proceed normally without a specific optimization lens.

### 3c. Clarify (if needed)

Ask at most 1-2 clarifying questions about the feature using AskUserQuestion. Only ask if there's genuine ambiguity. Examples of good clarifying questions:
- "Should this page be accessible to logged-in users only, or public?"
- "Does this feature replace the current X, or is it a new addition?"

Do NOT ask clarifying questions about visual styling — this is a UX wireframe, not a design comp.

### 3d. Generate the HTML File

Create a single self-contained HTML file at `wireframe/<feature-name>.html` where `<feature-name>` is a kebab-case slug derived from the feature description.

The HTML file MUST follow these rules:

#### Structure
- All CSS inline in a `<style>` tag in `<head>`
- All JS inline in a `<script>` tag before `</body>`
- No external dependencies (no CDN links, no fonts, no icons libraries)
- Use system fonts only: `-apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif`

#### Color Palette (STRICT — no other colors allowed)
- `#000000` — primary text, borders, headings
- `#333333` — secondary text
- `#666666` — tertiary text, labels
- `#999999` — placeholder text, disabled states
- `#cccccc` — borders, dividers
- `#eeeeee` — subtle backgrounds, hover states
- `#ffffff` — primary background
- No colors. No brand colors. No blues, reds, greens. Pure black-and-white with structural grays.

#### Page Layout
```
┌─────────────────────────────────────────────────┐
│  WIREFRAME: [Feature Name]                       │
│  Generated for [Project Name]                    │
│                                                   │
│  ┌───────────────┬─────────┬─────────┬─────────┐ │
│  │ A: Safe Option│Option B │Option C │Option D │ │
│  └───────────────┴─────────┴─────────┴─────────┘ │
│                                                   │
│  ═══════════════════════════════════════════════  │
│                                                   │
│  OPTION A: Safe Option                            │
│  [Extends existing design system patterns]        │
│  ┌─────────────────────────────────────────┐     │
│  │                                         │     │
│  │  [Interactive wireframe using existing  │     │
│  │   patterns from design-context.md]      │     │
│  │                                         │     │
│  └─────────────────────────────────────────┘     │
│                                                   │
│  ═══════════════════════════════════════════════  │
│                                                   │
│  OPTION B: [Philosophy Name]                      │
│  [Description of UX approach]                     │
│  ┌─────────────────────────────────────────┐     │
│  │                                         │     │
│  │  [Interactive wireframe]                │     │
│  │                                         │     │
│  └─────────────────────────────────────────┘     │
│                                                   │
│  ... more exploratory options ...                 │
└─────────────────────────────────────────────────┘
```

#### Required Sections in Each Option
1. **Title**: "Option [Letter]: [Philosophy Name]" (e.g., "Option B: Progressive Disclosure")
2. **Philosophy description**: 1-2 sentences explaining the UX approach and why it might work
3. **The wireframe itself**: Full interactive mockup with realistic placeholder content
4. **Pros/Cons**: Brief bullet points for each approach

**Option A: Safe Option** — Option A is always titled "Option A: Safe Option". It must use the layout structures, navigation patterns, interaction conventions, and component styles already documented in `design-context.md`. It should feel like a natural extension of the existing app — no new paradigms, no unfamiliar patterns. This is the low-risk baseline that the team could ship with confidence.

#### Interactivity Requirements
Each wireframe option should include functional interactive elements where appropriate:
- Clickable tabs, accordions, or expandable sections
- Form inputs that can be typed into
- Buttons with hover states (using only the allowed grays)
- Toggleable states (show/hide, active/inactive)
- Responsive behavior (the wireframes should work at different viewport widths)

#### UX Approaches

**Option A** always uses the **Safe Option** approach: replicate the existing design system's patterns from `design-context.md`. Use the same layout structures, navigation, component styles, and interaction conventions already in the app. No new paradigms.

**Options B, C, D+** — pick 3+ from these philosophies (or create your own):
- **Progressive Disclosure**: Start simple, reveal complexity on demand
- **Dashboard-First**: Everything visible at a glance with data density
- **Wizard/Step-by-Step**: Guide users through a sequential flow
- **Hub-and-Spoke**: Central overview with drill-down into details
- **Inline Editing**: Edit in place without mode switches
- **Split View**: Side-by-side comparison or master-detail
- **Card-Based**: Modular, scannable card layout
- **Conversational**: Chat-like or Q&A-style interaction
- **Kanban/Column**: Column-based organization
- **Timeline**: Chronological or sequential presentation
- **Search-First**: Search as primary navigation pattern
- **Contextual Actions**: Actions appear near related content

#### Content Guidelines
- Use realistic placeholder content relevant to the project (not lorem ipsum)
- Include realistic data quantities (show what it looks like with 5 items, 50 items)
- Show empty states, loading states, or error states where relevant
- Label interactive elements clearly (what happens when you click?)

#### Annotation System
Include a subtle annotation layer using small numbered circles (e.g., ①②③) linked to UX notes below each wireframe explaining key interaction decisions.

### 3e. Report to User

After generating the wireframe, tell the user:
- The file path: `wireframe/<feature-name>.html`
- How many options were generated (1 safe + N exploratory)
- A brief summary of each option's UX philosophy, noting that Option A is the safe baseline consistent with the existing design system
- If an optimization goal was provided, highlight which option(s) best serve that goal
- Suggest opening in a browser to interact with the prototypes

## Step 4: Update Design Context

After generating wireframes, check if the feature reveals new patterns or page types not yet documented in `wireframe/brain/design-context.md`. If so, append the new information to the appropriate section.

## Important Reminders

- You are a UX architect, not a visual designer. Focus on information architecture, user flows, interaction patterns, and content hierarchy.
- Option A (Safe Option) must always use existing patterns from `design-context.md`. Options B onwards must each represent a genuinely different UX philosophy — not just visual rearrangements.
- The wireframes must be functional HTML that works in a browser — not just pictures.
- Keep the black-and-white constraint strict. This forces focus on structure over aesthetics.
- Read the design context every time to maintain consistency across wireframe sessions.
- When the project has existing UX patterns, reference them in your wireframes where appropriate.
