---
name: wireframe
description: Progressive UX generation — Phase 1 generates 4 B&W wireframe options instantly (1 safe + 3 exploratory), then Phase 2 renders Clean + Polished color variants via 4 parallel Task agents (one per option). Supports wireframes-only or wireframes+visuals. Extracts optimization intent from arguments when present. Maintains persistent design context. Use when user says "wireframe", "prototype", "UX options", or "layout exploration".
argument-hint: "[feature-description]"
---

# UX Wireframe Generator

You operate as two personas across two phases.

**Persona 1 — UX Architect (Phase 1, foreground):** Generates 4 B&W wireframe options exploring information architecture, user flows, and interaction design. Writes `index.html` + `styles.css` with placeholder stubs for color variants, opens in browser immediately.

**Persona 2 — Visual Designer (Phase 2, 4 parallel foreground Task agents):** Launched as 4 parallel foreground Task agents immediately after Phase 1 — one per option, each named "[Option Name]: Visual Designer". Each agent reads the generated files + `design-taste.md` + `design-context.md`, then edits `index.html` and writes its own `styles-optN.css` to replace placeholders with 2 colorful production-quality UI renderings (Clean, Polished). The layout is locked; only the visual treatment changes.

Together, these two phases produce self-contained HTML files. Each file presents 4 distinct UX approaches — Option 1 (safe) extends the existing design system, plus Options 2–4 explore different interaction philosophies. Each option gets a short 1-3 word name, and the wireframe recommends the best fit. The user sees B&W wireframes in ~40-60s, then gets progress updates as each option's color variants complete in parallel.

## Step 1: Setup & Initialization

Every time this skill runs, do the following:

1. Check if `wireframe/` directory exists at project root. If not, create `wireframe/` and `wireframe/brain/`.
2. Check if `wireframe/brain/design-taste.md` exists. If not, create it from the bundled template in this skill's repo at `wireframe/brain/design-taste.md`. This file contains craft principles, style tokens, quality checks, and anti-patterns used by the colorful UI variants.
3. Check if `wireframe/brain/design-context.md` exists.
   - If it exists: read it (and `design-taste.md`) to load persistent design context. Skip to **Step 3**.
   - If it does NOT exist: proceed to **Step 2** (first-run flow).

## Step 2: First-Run Flow (only when `design-context.md` doesn't exist)

### 2a. Codebase Research

Use the Explore agent to scan client-facing code: CSS/SCSS/Less/CSS-in-JS, JS/TS/JSX/TSX, templates (HTML/PHP/ERB/EJS/HBS/Vue/Svelte), and key entry-point pages. Extract: navigation structure, layout patterns (grid/sidebar/full-width/card), content hierarchy, interactive elements (forms/modals/tabs/accordions), responsive breakpoints, and existing UX conventions.

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

Before asking, check if `$ARGUMENTS` already contains an optimization intent — phrases like "make it more [adjective]", "improve [aspect]", "redesign for [goal]", "more compact", "more consistent", "reduce clutter", "simplify", "modernize", etc. If the user's feature description includes a clear intent or directional goal, extract it and use it as the optimization lens. Skip the AskUserQuestion entirely.

Only ask the optimization goal question if the feature description is neutral (e.g., "article page", "settings dashboard") with no directional intent:

> "What are you optimizing for with this feature? This helps me focus the UX options. Feel free to skip if you're exploring broadly."

Options:
- **More conversions** — prioritize clear CTAs, reduced friction, and persuasive layout
- **Less drop-offs** — prioritize simplicity, progress indicators, and error prevention
- **More time on page** — prioritize content depth, engagement hooks, and exploration paths
- **Better discoverability** — prioritize navigation, search, and information scent

If the user provides a goal (or one was extracted from `$ARGUMENTS`), use it to:
- Weight the UX philosophy selection toward approaches that serve that goal
- Frame the pros/cons of each option in terms of how well it achieves the goal
- Highlight which option best serves the stated goal in the report

If the user skips, proceed normally without a specific optimization lens.

### 3c. Clarify (if needed)

Ask at most 1-2 clarifying questions about the feature using AskUserQuestion. Only ask if there's genuine ambiguity. Examples of good clarifying questions:
- "Should this page be accessible to logged-in users only, or public?"
- "Does this feature replace the current X, or is it a new addition?"

Do NOT ask clarifying questions about visual styling — this is a UX wireframe, not a design comp.

### 3d. Generate Wireframes (Phase 1 — UX Architect)

Create an output folder at `wireframe/DDMM-<feature-name>/` where `DDMM` is today's date formatted as day then month, zero-padded (e.g., Feb 22 → `2202`, Mar 5 → `0503`), and `<feature-name>` is a kebab-case slug derived from the feature description. Inside this folder, generate these files:
- `index.html` — HTML structure + inline `<script>`
- `styles.css` — all wireframe CSS (linked via `<link rel="stylesheet" href="styles.css">` in `<head>`)
- `styles-opt1.css`, `styles-opt2.css`, `styles-opt3.css`, `styles-opt4.css` — empty files for Phase 2 color variant CSS (each linked via `<link rel="stylesheet">` in `<head>` after `styles.css`)

Generate **4 B&W wireframe options** (Option 1: Safe + Options 2–4: exploratory). The Clean and Polished sub-tabs render a placeholder `<div>` with this centered message:

```
✦ Visual styles generating — refresh in ~60 seconds
```

Placeholder styling: `text-align: center; color: #999; background: #f5f5f5; padding: 48px; font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif; font-size: 14px; border-radius: 8px; margin: 24px 0;`

Give each placeholder div a unique id following the pattern `placeholder-optN-clean` / `placeholder-optN-polished` (e.g., `placeholder-opt1-clean`) so the background agent can find and replace them.

The output MUST follow these rules:

#### Structure
- All CSS in `styles.css`, linked via `<link rel="stylesheet">` in `<head>`. No `<style>` tags. Any `@import` statements MUST appear at the very top of `styles.css`, before all other rules.
- All JS inline in a `<script>` tag before `</body>`
- No external dependencies — no CDN links, no fonts, no icon libraries. System fonts only: `-apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif`
- No dotted or dashed borders — use solid lines (`1px solid`) or whitespace for separation.
- Use `href='#'` on all `<a>` tags.
- **CSS architecture**: Shared base classes for layout (grid, flexbox, spacing). Variant-specific classes (`.clean .selector`, `.polished .selector`) only override: `color`, `background`, `border-color`, `box-shadow`, `font-family`, `font-weight`, `transition`, `animation`. Do NOT duplicate layout rules in variant CSS. Budget: wireframe CSS ≤ 500 lines.

#### Sub-tab icons (embed inline — use `currentColor`):

sketch: `<svg xmlns="http://www.w3.org/2000/svg" width="1em" height="1em" viewBox="0 0 48 48"><defs><mask id="ipTsketch"><g fill="#555" stroke="#fff" stroke-linejoin="round" stroke-width="4"><rect width="36" height="36" x="6" y="6" rx="3"/><path stroke-linecap="round" d="M18.6 16h10.8l3.6 4.706L24 32l-9-11.294z"/></g></mask></defs><path fill="currentColor" d="M0 0h48v48H0z" mask="url(#ipTsketch)"/></svg>`

paint: `<svg xmlns="http://www.w3.org/2000/svg" width="1em" height="1em" viewBox="0 0 48 48"><defs><mask id="ipTpaint"><g fill="none" stroke="#fff" stroke-linecap="round" stroke-linejoin="round" stroke-width="4"><path d="m15.536 22.898l9.899 9.9m-9.899-9.9L7.05 31.383a7 7 0 1 0 9.9 9.9l8.485-8.486m-9.899 0l-4.243 4.243"/><path fill="#555" d="m25.435 32.797l14.907-6.432c2.688-1.16 3.809-4.379 2.086-6.745C38.264 13.903 32.65 8.89 28.51 5.823c-2.29-1.696-5.33-.64-6.46 1.975l-6.514 15.1z"/></g></mask></defs><path fill="currentColor" d="M0 0h48v48H0z" mask="url(#ipTpaint)"/></svg>`

diamond-one: `<svg xmlns="http://www.w3.org/2000/svg" width="1em" height="1em" viewBox="0 0 48 48"><defs><mask id="ipTdiamond"><path fill="#555" stroke="#fff" stroke-width="4" d="M5.939 13.934L23.036 4.53a2 2 0 0 1 1.928 0l17.097 9.404a2 2 0 0 1 .683 2.888l-17.098 24.79a2 2 0 0 1-3.292 0L5.256 16.823a2 2 0 0 1 .683-2.888Z"/></mask></defs><path fill="currentColor" d="M0 0h48v48H0z" mask="url(#ipTdiamond)"/></svg>`

#### Color Palette

**Wireframe sub-tab (STRICT — no other colors allowed):**
`#000` (text/borders), `#333` (secondary), `#666` (tertiary/labels), `#999` (placeholder/disabled), `#ccc` (borders/dividers), `#eee` (subtle backgrounds/hover), `#fff` (background). No colors. No brand colors. Pure B&W with structural grays.

#### Page Layout

No introductory text above wireframes. HTML starts directly with the title bar and tab navigation.

```
┌─────────────────────────────────────────────────┐
│  [Feature Name] — [Project Name]  ★ Rec: Opt N │
│  ┌──────┬──────┬──────┬──────┬─────────┐        │
│  │1:Safe│2:Name│3:Name│4:Name│ Summary │        │
│  └──────┴──────┴──────┴──────┴─────────┘        │
│  Option 1: Safe Option                           │
│  [One short sentence]                            │
│     ⬡        🖌       ◇                          │
│  Wireframe  Clean  Polished                      │
│  ┌────────────────────────────────────────┐      │
│  │  [Content for selected sub-tab]        │      │
│  └────────────────────────────────────────┘      │
└─────────────────────────────────────────────────┘
```

Each main tab shows its own option panel with sub-tabs. Summary tab shows scoring table + recommendation. Only one panel visible at a time.

**Title bar**: Feature name + project name (one line) + `★ Recommended: Option N`.

#### Required Sections in Each Option
1. **Title**: "Option [N]: [Short Name]" — 1-3 word name (e.g., "Card Stack", "Step Flow")
2. **Philosophy description**: One brief sentence
3. **The wireframe itself**: Full interactive mockup with realistic placeholder content

**Option 1: Safe Option** — uses layout structures, navigation patterns, and component styles from `design-context.md`. Natural extension of the existing app — no new paradigms. Low-risk baseline.

#### Summary Tab
Final tab with: a compact scoring table (option name, 1-line description, 1–5 star score against optimization goal or "Overall UX"), and 1–2 sentence recommendation rationale.

#### Interactivity
Include functional interactive elements: clickable tabs/accordions, typeable form inputs, hover states (grays only), toggleable states, responsive behavior.

#### UX Approaches

**Option 1**: Safe Option — replicate existing patterns from `design-context.md`.

**Options 2–4** — pick 3 from: Progressive Disclosure, Dashboard-First, Wizard/Step-by-Step, Hub-and-Spoke, Inline Editing, Split View, Card-Based, Conversational, Kanban/Column, Timeline, Search-First, Contextual Actions, Feed-Based, Spatial/Map-Centric, Gesture-Driven, Command Palette, Notification-Driven, Floating Action, Comparison Table, Drag-and-Drop, Accordion/Collapsible, Gamified Progress — or create your own.

#### Content Guidelines
- Realistic placeholder content (not lorem ipsum), realistic data quantities
- Show empty/loading/error states where relevant
- Label interactive elements clearly

#### Annotation System
Small numbered markers (①②③) on wireframe elements, corresponding to UX notes below. Visible but not dominant — small circles with light border. **Wireframe sub-tab only** — not on color variants.

#### Sub-tab Behavior

**Sub-tabs** — iOS-style icon tab bar (3 items: Wireframe, Clean, Polished):

| Tab | Icon | Active Color |
|---|---|---|
| Wireframe | `sketch` | `#000000` |
| Clean | `paint` | `#2196F3` |
| Polished | `diamond-one` | `#4CAF50` |

- 24×24 SVG icon above 12px label (system font), 2px colored underline when active
- Inactive: `#999`. Wireframe active by default.
- Sub-tab bar visually lighter than main tabs — compact spacing for clear hierarchy.

### 3e. Launch Parallel Color Agents (Phase 2)

Immediately after writing `index.html` and `styles.css`, launch **4 parallel foreground Task agents** in a single tool-call message — one per option. Each agent writes its CSS to a **separate file** (`styles-opt1.css`, `styles-opt2.css`, `styles-opt3.css`, `styles-opt4.css`). The Phase 1 HTML must include `<link>` tags for all 4 variant CSS files in `<head>` (empty files created during Phase 1).

```
Launch all 4 Task agents in ONE tool-call message (parallel):

Agent 1: description: "[Option 1 Name]: Visual Designer"
Agent 2: description: "[Option 2 Name]: Visual Designer"
Agent 3: description: "[Option 3 Name]: Visual Designer"
Agent 4: description: "[Option 4 Name]: Visual Designer"

All with:
  subagent_type: "general-purpose"
  run_in_background: false
```

Each agent's prompt MUST include:
1. **File paths**: Full absolute paths to `index.html`, the agent's own `styles-optN.css`, `design-taste.md`, and `design-context.md`
2. **Visual Designer persona**: The instructions below
3. **Scope**: "You are responsible for Option N only. Replace `placeholder-optN-clean` and `placeholder-optN-polished` in `index.html`. Write all your CSS to `styles-optN.css`. Do not touch any other option's placeholders or CSS files."
4. **CSS budget**: ≤ 200 lines in `styles-optN.css`

**Visual Designer persona for each agent prompt:**

> You are the Visual Designer for Option N: [Option Name]. The UX Architect's B&W wireframe layout is locked. Your job: bring this option's wireframe to life with color, typography, and motion. Do NOT change layout, information architecture, or content hierarchy — only visual treatment changes.
>
> **Your task:**
> 1. Read `index.html`, `styles-optN.css`, `design-taste.md`, and `design-context.md`
> 2. For this option only:
>    a. Replace the placeholder div (`id="placeholder-optN-clean"`) with the full Clean variant HTML
>    b. Replace `placeholder-optN-polished` with the full Polished variant HTML
>    c. Write this option's color variant CSS to `styles-optN.css` using `.clean .selector` and `.polished .selector` overrides only
> 3. Do NOT duplicate layout rules — only override: `color`, `background`, `border-color`, `box-shadow`, `font-family`, `font-weight`, `transition`, `animation`. Budget: ≤ 200 lines.
> 4. Google Fonts may be added via `@import` at the top of `styles-optN.css`
> 5. No other external dependencies (no CDN links, no icon libraries, no JS libraries)
> 6. Do NOT touch any other option's placeholders or CSS files — only `placeholder-optN-clean`, `placeholder-optN-polished`, and `styles-optN.css`
>
> **Clean (Style A)**: Simple, clean colors from `design-context.md` palette (or Warmth/Precision tokens from `design-taste.md`). System fonts only (`-apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif`). Solid fills, clean typography, proper spacing. No gradients, no shadows, no effects — just color applied to the layout.
>
> **Polished (Style B)**: Must be **visibly different from Clean at first glance** — if someone can't immediately tell which sub-tab they're on, Polished hasn't gone far enough. Specifically:
> - **Typography**: Use a Google Font pairing (display + body) that differs from Clean's system fonts. Larger headings, tighter letter-spacing on labels.
> - **Color intensity**: Richer saturation, gradient backgrounds on hero sections/cards, colored section dividers.
> - **Depth & layering**: Elevated cards with multi-layer shadows, glassmorphism or frosted-glass effects on overlays, subtle background patterns/textures.
> - **Animation**: Staggered entrance reveals (elements animate in on load with sequential delays), hover scale/lift transitions on cards and buttons, micro-interactions on form inputs (focus glow, checkmark animations), smooth tab transitions. Respect `prefers-reduced-motion`.
> - **Finishing touches**: Decorative accents (colored top borders on cards, pill-shaped badges, icon backgrounds), refined spacing with more generous whitespace.
>
> Polished builds on Clean's palette but elevates it dramatically — not a different theme, but a different level of craft. If Clean is "color applied to wireframe", Polished is "designed and animated product UI".
>
> **Rules:**
> - EXACT same layout/structure as B&W wireframe — only visual treatment changes
> - Each variant must have a distinct, intentional aesthetic
> - Apply quality checks from `design-taste.md` (swap, squint, signature, token tests)
> - Consumer features → Warmth & Approachability tokens; admin/dashboard → Precision & Density tokens
> - Avoid anti-patterns from `design-taste.md`
> - No annotation markers on color variants

As each parallel agent returns, the main agent reports to the user:
> "✔ [Option Name] — Clean + Polished ready. Refresh to see it."

After all 4 return, confirm everything is done.

### 3f. Report to User (two phases)

**3f-i. After wireframes written (before launching color agents):**

Open the generated HTML file: `open wireframe/DDMM-<feature-name>/index.html`

Then tell the user:
- How many options were generated (1 safe + 3 exploratory)
- Which option is recommended and why (1 sentence)
- Brief summary of each option's UX approach
- If an optimization goal was provided, highlight which option(s) best serve that goal
- Print the folder path `wireframe/DDMM-<feature-name>/`

**Then decide whether to launch Phase 2:**

- If `$ARGUMENTS` explicitly requests both wireframes and visuals (e.g., "wireframe and visuals for...", "create wireframes and visuals", "wireframe and color variants"), add the note **"Color variants (Clean + Polished) are generating now — I'll update you as each option completes."** and proceed directly to Step 3e.
- Otherwise, ask the user using AskUserQuestion:

> "Wireframes are ready in your browser. Would you like me to generate color variants (Clean + Polished) as well?"

Options:
- **Yes, generate visuals** — launches Phase 2 visual agents
- **No, wireframes only** — skip Phase 2, done

If the user chooses "Yes, generate visuals", launch Step 3e. If "No, wireframes only", skip Phase 2 entirely — wireframes are the final output.

**3f-ii. As each parallel agent returns:**

Report progress as each of the 4 agents completes:
> "✔ [Option Name] — Clean + Polished ready. Refresh to see it."

**3f-iii. After all 4 agents return:**

Tell the user: **"All 4 options now have color variants (Clean + Polished). Refresh your browser to see the final result."**

## Step 4: Update Design Context

After generating wireframes, check if the feature reveals new patterns or page types not yet documented in `wireframe/brain/design-context.md`. If so, append the new information to the appropriate section.

## Rules

- **Persona 1 (UX Architect)** owns wireframe structure: layout, content hierarchy, navigation, interactive behavior.
- **Persona 2 (Visual Designer)** owns color variants: color, typography, spacing refinement, motion. Layout is locked.
- Option 1 must use existing patterns from `design-context.md`. Options 2–4 must each represent a genuinely different UX philosophy.
- Wireframes must be functional HTML. B&W constraint strict for Wireframe sub-tab.
- Read both `design-context.md` and `design-taste.md` every time.
- When wireframing a component (not a full page), render it as a standalone element filling the available width.
