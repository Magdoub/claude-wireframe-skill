---
name: wireframe
description: Two-phase UX generation — a UX Architect creates B&W wireframes exploring information architecture, then a Visual Designer renders 2 colorful production-quality UI variants per wireframe. Creates 4+ distinct UX approaches per feature (Option 1: safe + Options 2-4+: exploratory) with different interaction/layout philosophies, a recommended pick, and short option names. Maintains persistent design context. Use when user says "wireframe", "prototype", "UX options", or "layout exploration".
argument-hint: "[feature-description]"
disable-model-invocation: true
---

# UX Wireframe Generator

You operate as two personas in sequence.

**Persona 1 — UX Architect:** Generates B&W wireframes exploring information architecture, user flows, and interaction design. The UX Architect owns structure — layout, content hierarchy, navigation, and interactive behavior. All wireframe sub-tabs are produced in this phase.

**Persona 2 — Visual Designer:** Takes each completed wireframe and creates 2 colorful, production-quality UI renderings of that same layout (Clean, Polished). The Visual Designer owns aesthetic craft — color, typography, spacing refinement, and motion. The layout is locked; only the visual treatment changes.

Together, these two personas produce self-contained HTML files. Each file presents 4+ distinct UX approaches — Option 1 (safe) extends the existing design system, plus Options 2-4+ explore different interaction philosophies. Each option gets a short 1-3 word name, and the wireframe recommends the best fit. Every option includes a black-and-white wireframe plus 2 colorful UI renderings — so users can see what each wireframe would look like as a real, styled interface.

## Step 1: Setup & Initialization

Every time this skill runs, do the following:

1. Check if `wireframe/` directory exists at project root. If not, create `wireframe/` and `wireframe/brain/`.
2. Check if `wireframe/brain/design-taste.md` exists. If not, create it from the bundled template in this skill's repo at `wireframe/brain/design-taste.md`. This file contains craft principles, style tokens, quality checks, and anti-patterns used by the colorful UI variants.
3. Check if `wireframe/brain/design-context.md` exists.
   - If it exists: read it (and `design-taste.md`) to load persistent design context. Skip to **Step 3**.
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

If the codebase research and screenshots don't make the target platform obvious (mobile app vs desktop/web vs responsive), ask the user using AskUserQuestion:

> "What's the target platform for this product?"

Options:
- **Mobile** — designing primarily for mobile screens
- **Desktop / Web** — designing primarily for desktop or web browsers
- **Both (responsive)** — designing for both with responsive layouts

Save the answer and use it in **Step 2c** under `## Target Platform`.

### 2c. Write Design Context

Create `wireframe/brain/design-context.md` with this structure:

```markdown
# Design Context — [Project Name]

Generated: [date]

## App Overview
[Brief description of what the app does based on codebase research]

## Target Platform
[Mobile / Desktop / Web / Both (responsive) — based on codebase research, screenshots, or user input]

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

Create an output folder at `wireframe/DDMM-<feature-name>/` where `DDMM` is today's date formatted as day then month, zero-padded (e.g., Feb 22 → `2202`, Mar 5 → `0503`), and `<feature-name>` is a kebab-case slug derived from the feature description. Inside this folder, generate two files:
- `index.html` — HTML structure + inline `<script>`
- `styles.css` — all CSS (linked via `<link rel="stylesheet" href="styles.css">` in `<head>`)

The output MUST follow these rules:

#### Structure
- All CSS in a separate `styles.css` file in the same folder, linked via `<link rel="stylesheet" href="styles.css">` in `<head>`. Do NOT use a `<style>` tag in the HTML.
- All JS inline in a `<script>` tag before `</body>`
- **Wireframe sub-tab**: No external dependencies (no CDN links, no fonts, no icons libraries). Use system fonts only: `-apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif`
- **Color variant sub-tabs (Style A–B)**: May use Google Fonts via `@import` in the `styles.css` file. Still no other external dependencies (no CDN links, no icon libraries, no JS libraries).
- **Sub-tab icons**: Each style sub-tab uses an icon from the `icon-park-twotone` set. The SVG markup for all 3 icons is provided below — embed them inline in the HTML. Do NOT load icons from a CDN at runtime.
- **No dotted or dashed lines**: Do not use dotted or dashed borders, dividers, or separator lines anywhere in the output. Use solid lines (`1px solid`) or whitespace/padding for visual separation instead.

#### Color Palette

**Wireframe sub-tab (STRICT — no other colors allowed):**
- `#000000` — primary text, borders, headings
- `#333333` — secondary text
- `#666666` — tertiary text, labels
- `#999999` — placeholder text, disabled states
- `#cccccc` — borders, dividers
- `#eeeeee` — subtle backgrounds, hover states
- `#ffffff` — primary background
- No colors. No brand colors. No blues, reds, greens. Pure black-and-white with structural grays.

**Color variant sub-tabs (Style A–B):** Each variant defines its own palette, guided by `wireframe/brain/design-taste.md`. Use the Warmth & Approachability tokens for consumer-facing features, Precision & Density tokens for admin/dashboard features. Each variant's palette should be distinct and intentional — see the Colorful UI Variants section below for per-variant direction.

#### Page Layout

Do NOT generate any introductory text, preamble, or explanation above the wireframe options. The HTML file starts directly with the title bar and tab navigation — nothing else.

```
┌─────────────────────────────────────────────────┐
│  [Feature Name] — [Project Name]  ★ Rec: Opt N │
│                                                  │
│  ┌────────┬─────────┬─────────┬─────────┐       │
│  │1: Safe │2: [Name]│3: [Name]│ Summary │       │
│  └────────┴─────────┴─────────┴─────────┘       │
│                                                  │
│  Option 1: Safe Option  ← (shown for tab 1)     │
│  [One short sentence]                            │
│                                                  │
│     ⬡           🖌          ◇                    │
│  Wireframe    Clean     Polished                 │
│  ─────────                                       │
│  ┌────────────────────────────────────────┐      │
│  │                                        │      │
│  │  [Content for selected sub-tab]        │      │
│  │  Wireframe: B&W wireframe (default)    │      │
│  │  Style A-B: Colorful UI renderings     │      │
│  │                                        │      │
│  └────────────────────────────────────────┘      │
│                                                  │
└─────────────────────────────────────────────────┘
```

Each main tab (1: Safe, 2: [Name], etc.) shows its own option panel with its own sub-tabs. The Summary tab shows the scoring table + recommendation. Only one panel is visible at a time — clicking a main tab switches which option is displayed.

#### Title Bar
The title bar at the top of the page contains:
- The feature name and project name (compact, one line)
- A recommended option indicator: `★ Recommended: Option N` — pick the option that best serves the user's optimization goal. If no goal was provided, pick the option with the best overall UX balance.

#### Required Sections in Each Option
1. **Title**: "Option [Number]: [Short Name]" — the short name is 1-3 words (e.g., "Option 2: Card Stack", "Option 3: Step Flow")
2. **Philosophy description**: One brief sentence explaining the approach
3. **The wireframe itself**: Full interactive mockup with realistic placeholder content

**Option 1: Safe Option** — Option 1 is always titled "Option 1: Safe Option". It must use the layout structures, navigation patterns, interaction conventions, and component styles already documented in `design-context.md`. It should feel like a natural extension of the existing app — no new paradigms, no unfamiliar patterns. This is the low-risk baseline that the team could ship with confidence.

#### Summary Tab
The Summary tab is the final tab. It provides a comparative overview of all options:

- **Scoring table**: A compact HTML table with one row per option. Columns: Option name, a brief 1-line description, and a score (1–5 stars) against the optimization goal (or "Overall UX" if no goal was provided).
- **Recommended pick**: 1–2 sentences explaining why the recommended option was chosen, referencing the optimization goal (or overall UX balance if no goal).

Keep it brief — just the table and the recommendation rationale, no lengthy prose.

#### Interactivity Requirements
Each wireframe option should include functional interactive elements where appropriate:
- Clickable tabs, accordions, or expandable sections
- Form inputs that can be typed into
- Buttons with hover states (using only the allowed grays)
- Toggleable states (show/hide, active/inactive)
- Responsive behavior (the wireframes should work at different viewport widths)

#### UX Approaches

**Option 1** always uses the **Safe Option** approach: replicate the existing design system's patterns from `design-context.md`. Use the same layout structures, navigation, component styles, and interaction conventions already in the app. No new paradigms.

**Options 2, 3, 4+** — pick 3+ from these philosophies (or create your own):
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
- **Feed-Based**: Content surfaces in a continuous scrollable stream optimized for browsing
- **Spatial/Map-Centric**: Navigation anchored to geographic or visual spatial relationships
- **Gesture-Driven**: Primary interactions via swipe, pull, pinch rather than buttons
- **Command Palette**: Keyboard-first searchable access to all actions (CMD+K pattern)
- **Notification-Driven**: Interface organized around alerts and updates that prompt action
- **Floating Action**: Persistent primary action button that morphs to reveal contextual options
- **Comparison Table**: Side-by-side feature/attribute matrix for evaluating options
- **Drag-and-Drop**: Direct manipulation to reorder, organize, or assign items
- **Accordion/Collapsible**: Vertically stacked expandable sections to manage density
- **Gamified Progress**: Achievement markers, progress bars, and streaks to drive engagement

#### Content Guidelines
- Use realistic placeholder content relevant to the project (not lorem ipsum)
- Include realistic data quantities (show what it looks like with 5 items, 50 items)
- Show empty states, loading states, or error states where relevant
- Label interactive elements clearly (what happens when you click?)

#### Annotation System
Include a subtle annotation layer using small numbered circles (e.g., ①②③) linked to UX notes below each wireframe explaining key interaction decisions.

#### Colorful UI Variants

> **--- Persona Switch: Visual Designer ---**
> You are now the Visual Designer. The UX Architect's work is done — the B&W wireframe layout is locked. Your job is to bring each wireframe to life with color, typography, and motion. Do not change the layout, information architecture, or content hierarchy. Only the visual treatment changes.

Each wireframe option includes a secondary row of sub-tabs below the option title and description:

**Sub-tabs** — displayed as an iOS-style icon tab bar:

| Tab | Icon | Active Color | Hex |
|---|---|---|---|
| Wireframe | `sketch` | Black | `#000000` |
| Clean | `paint` | Blue | `#2196F3` |
| Polished | `diamond-one` | Green | `#4CAF50` |

**Tab bar behavior:**
- Layout: horizontal bar, 3 equally-spaced items, each showing a 24×24 SVG icon above a 12px label (system font)
- Active tab: icon + label in the tab's signature color, plus a 2px colored underline bar beneath
- Inactive tabs: icon + label in `#999999`
- "Wireframe" is active by default when switching to any option
- Clicking a tab colors it and grays out the others

**Inline SVG icons** (embed these exactly in the generated HTML — they use `currentColor` so set `color` on the parent to control icon color):

sketch:
```svg
<svg xmlns="http://www.w3.org/2000/svg" width="1em" height="1em" viewBox="0 0 48 48"><defs><mask id="ipTsketch"><g fill="#555" stroke="#fff" stroke-linejoin="round" stroke-width="4"><rect width="36" height="36" x="6" y="6" rx="3"/><path stroke-linecap="round" d="M18.6 16h10.8l3.6 4.706L24 32l-9-11.294z"/></g></mask></defs><path fill="currentColor" d="M0 0h48v48H0z" mask="url(#ipTsketch)"/></svg>
```

paint:
```svg
<svg xmlns="http://www.w3.org/2000/svg" width="1em" height="1em" viewBox="0 0 48 48"><defs><mask id="ipTpaint"><g fill="none" stroke="#fff" stroke-linecap="round" stroke-linejoin="round" stroke-width="4"><path d="m15.536 22.898l9.899 9.9m-9.899-9.9L7.05 31.383a7 7 0 1 0 9.9 9.9l8.485-8.486m-9.899 0l-4.243 4.243"/><path fill="#555" d="m25.435 32.797l14.907-6.432c2.688-1.16 3.809-4.379 2.086-6.745C38.264 13.903 32.65 8.89 28.51 5.823c-2.29-1.696-5.33-.64-6.46 1.975l-6.514 15.1z"/></g></mask></defs><path fill="currentColor" d="M0 0h48v48H0z" mask="url(#ipTpaint)"/></svg>
```

diamond-one:
```svg
<svg xmlns="http://www.w3.org/2000/svg" width="1em" height="1em" viewBox="0 0 48 48"><defs><mask id="ipTdiamond"><path fill="#555" stroke="#fff" stroke-width="4" d="M5.939 13.934L23.036 4.53a2 2 0 0 1 1.928 0l17.097 9.404a2 2 0 0 1 .683 2.888l-17.098 24.79a2 2 0 0 1-3.292 0L5.256 16.823a2 2 0 0 1 .683-2.888Z"/></mask></defs><path fill="currentColor" d="M0 0h48v48H0z" mask="url(#ipTdiamond)"/></svg>
```

- **Wireframe** (default active): The existing B&W wireframe — unchanged from the rules above. Uses strict grayscale palette, system fonts only, no external dependencies.

- **Clean (Style A)**: Simple, clean colors applied to the wireframe layout. Uses the project's color palette from `design-context.md` (or the appropriate style tokens from `design-taste.md`) with straightforward solid fills, clean typography, and proper spacing. No gradients, no fancy effects — just the layout brought to life with color. This is the "what if we just shipped this with real colors" variant.

- **Polished (Style B)**: Same design language as Clean but elevated and animated: bolder color contrasts, refined gradients, enhanced hover effects, richer accent colors. CSS animations included — staggered fade-in reveals on load, smooth hover transitions, micro-interactions on buttons/inputs (scale, color shift). All animations respect `prefers-reduced-motion`. This variant is premium, polished, and alive.

**Rules for all color variants (Style A–B):**
- Must use the **EXACT same layout/structure** as the B&W wireframe for that option. Same information architecture, same element positions, same content hierarchy. Only the visual treatment changes.
- Still fully self-contained — no external JS libraries, no icon CDNs. **May** use Google Fonts via `@import` in the `styles.css` file.
- Each variant must have a **distinct, intentional aesthetic** — not just "slightly different colors." If you can't articulate what makes each variant different in one sentence, it's not differentiated enough.
- Read `wireframe/brain/design-taste.md` for craft principles, style tokens, and anti-patterns.
- Apply the quality checks (swap test, squint test, signature test, token test) mentally before outputting each variant.
- Consumer-facing features should lean on the **Warmth & Approachability** tokens; admin/dashboard features on the **Precision & Density** tokens from `design-taste.md`.
- Avoid the anti-patterns listed in `design-taste.md` — especially "Inter + purple gradient on white," identical card grids, and gratuitous glassmorphism.
**Implementation:**
- The sub-tab switching is handled by the same JS that handles the main option tabs. Each sub-tab shows/hides a `<div>` containing that variant's content.
- The "Wireframe" sub-tab is active by default when switching to any option.
- Each variant's content is a complete rendering of the same layout — not a partial or placeholder.
- The sub-tab icon bar should be visually lighter than the main option tabs — 24px icons, 12px labels, compact spacing — to maintain clear visual hierarchy between option-level and style-variant navigation.

### 3e. Report to User

First, open the generated HTML file in the user's default browser using the Bash tool: `open wireframe/DDMM-<feature-name>/index.html`

Then tell the user:
- How many options were generated (1 safe + N exploratory)
- Which option is recommended and why (1 sentence)
- A brief summary of each option's UX approach, noting that Option 1 is the safe baseline
- If an optimization goal was provided, highlight which option(s) best serve that goal
- As the very last line of your reply, print the folder path: `wireframe/DDMM-<feature-name>/`

## Step 4: Update Design Context

After generating wireframes, check if the feature reveals new patterns or page types not yet documented in `wireframe/brain/design-context.md`. If so, append the new information to the appropriate section.

## Important Reminders

- **Persona 1 (UX Architect)** owns the Wireframe sub-tab: information architecture, user flows, layout structure, and interactive behavior. No visual design decisions happen here.
- **Persona 2 (Visual Designer)** owns the color variant sub-tabs (Style A–B): color, typography, spacing refinement, and motion. The wireframe layout is locked — the Visual Designer must not alter layout, content hierarchy, or information architecture. Only the visual treatment changes.
- Option 1 (Safe Option) must always use existing patterns from `design-context.md`. Options 2 onwards must each represent a genuinely different UX philosophy — not just visual rearrangements.
- The wireframes must be functional HTML that works in a browser — not just pictures.
- Keep the black-and-white constraint strict for the **Wireframe sub-tab**. Color variants follow their own palette rules guided by `design-taste.md`.
- Read both `design-context.md` and `design-taste.md` every time to maintain consistency across wireframe sessions.
- When the project has existing UX patterns, reference them in your wireframes where appropriate.
- Each of the 2 color variants must be genuinely distinct — apply the quality checks from `design-taste.md` and avoid the listed anti-patterns.
- When wireframing a component (not a full page), render it as a standalone element filling the available width.
