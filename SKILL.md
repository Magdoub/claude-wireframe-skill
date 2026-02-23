---
name: wireframe
description: Progressive UX generation — Phase 1 generates 5 B&W wireframe options instantly (1 safe + 4 exploratory), then Phase 2 renders Clean + Polished color variants via 5 parallel Task agents (one per option). Supports wireframes-only or wireframes+visuals. Extracts optimization intent from arguments when present. Maintains persistent design context. Use when user says "wireframe", "prototype", "UX options", or "layout exploration".
argument-hint: "[feature-description]"
---

# UX Wireframe Generator

You operate as two personas across two phases.

**Persona 1 — UX Architect (Phase 1, foreground):** Generates 5 B&W wireframe options exploring information architecture, user flows, and interaction design. Writes `index.html` + `styles.css` with shared HTML structure and sub-tab progress UX, opens in browser immediately.

**Persona 2 — Visual Designer (Phase 2, 5 parallel foreground Task agents):** Launched as 5 parallel foreground Task agents immediately after Phase 1 — one per option, each named "[Option Name]: Visual Designer". Each agent reads `index.html`, its own `styles-optN.css`, `design-taste.md`, and `design-context.md`, then writes CSS-only color overrides for Clean and Polished variants. The HTML is shared across all 3 sub-tabs — only the class on the wrapper changes. The layout is locked; only the visual treatment changes.

Together, these two phases produce self-contained HTML files. Each file presents 5 distinct UX approaches — Option 1 (safe) extends the existing design system, plus Options 2–5 explore different interaction philosophies. Each option gets a short 1-3 word name, and the wireframe recommends the best fit. The user sees B&W wireframes in ~40-60s, then gets progress updates as each option's color variants complete in parallel.

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

### 3b-ii. Scope: Wireframes only or wireframes + visuals?

If `$ARGUMENTS` explicitly requests both wireframes and visuals (e.g., "wireframe and visuals for...", "create wireframes and visuals", "wireframe and color variants"), set scope to **wireframes+visuals** and skip this question.

If `$ARGUMENTS` only says "wireframe" or doesn't mention visuals, ask using AskUserQuestion:

> "Would you like wireframes only, or wireframes with color variants (Clean + Polished)?"

Options:
- **Wireframes + visuals (Recommended)** — generates B&W wireframes, then Clean + Polished color variants
- **Wireframes only** — generates B&W wireframes only, skips color phase

Store the answer. Use it in Step 3f to decide whether to launch Phase 2.

### 3c. Clarify (if needed)

Ask at most 1-2 clarifying questions about the feature using AskUserQuestion. Only ask if there's genuine ambiguity. Examples of good clarifying questions:
- "Should this page be accessible to logged-in users only, or public?"
- "Does this feature replace the current X, or is it a new addition?"

Do NOT ask clarifying questions about visual styling — this is a UX wireframe, not a design comp.

### 3d. Generate Wireframes (Phase 1 — UX Architect)

Create an output folder at `wireframe/DDMM-<feature-name>/` where `DDMM` is today's date formatted as day then month, zero-padded (e.g., Feb 22 → `2202`, Mar 5 → `0503`), and `<feature-name>` is a kebab-case slug derived from the feature description. Inside this folder, generate these files:
- `index.html` — HTML structure + inline `<script>`
- `styles.css` — all wireframe CSS (linked via `<link rel="stylesheet" href="styles.css">` in `<head>`)
- `styles-opt1.css`, `styles-opt2.css`, `styles-opt3.css`, `styles-opt4.css`, `styles-opt5.css` — empty files for Phase 2 color variant CSS (each linked via `<link rel="stylesheet" href="styles-optN.css">` in `<head>` after `styles.css`)

Generate **5 B&W wireframe options** (Option 1: Safe + Options 2–5: exploratory). Phase 1 renders each option's content once inside a `<div class="browser-frame wireframe" id="frame-optN">`. Sub-tab JS toggles the wrapper class between `wireframe`, `clean`, and `polished` — no separate content panels. Annotations (`.wf-annotations`) are hidden via CSS when class is `clean` or `polished`.

The output MUST follow these rules:

#### Structure
- All CSS in `styles.css`, linked via `<link rel="stylesheet">` in `<head>`. No `<style>` tags. Any `@import` statements MUST appear at the very top of `styles.css`, before all other rules.
- All JS inline in a `<script>` tag before `</body>`
- No external dependencies — no CDN links, no fonts, no icon libraries. System fonts only: `-apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif`
- No dotted or dashed borders — use solid lines (`1px solid`) or whitespace for separation.
- Use `href='#'` on all `<a>` tags.
- **CSS architecture**: One HTML block per option shared across all 3 sub-tabs. The `browser-frame` wrapper gets a class toggled by JS: `.wireframe` (default), `.clean`, or `.polished`. Base layout in `styles.css`. Variant overrides in `styles-optN.css` using `.clean .selector` and `.polished .selector`. Annotations hidden via `.clean .wf-annotations, .polished .wf-annotations { display: none; }`. Browser chrome dots styled per variant via CSS. Do NOT create separate sub-panel divs for each variant — there is one panel per option. Variant-specific classes only override: `color`, `background`, `border-color`, `box-shadow`, `font-family`, `font-weight`, `transition`, `animation`. Do NOT duplicate layout rules in variant CSS. Budget: wireframe CSS ≤ 500 lines.
- **Annotation hiding rule** (MUST be in `styles.css`): `.clean .wf-annotations, .polished .wf-annotations { display: none; }` — hides the entire annotations block (markers + explanatory text) whenever the wrapper has `.clean` or `.polished` class.

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
│  ┌──────┬──────┬──────┬──────┬──────┬─────────┐  │
│  │1:Safe│2:Name│3:Name│4:Name│5:Name│ Summary │  │
│  └──────┴──────┴──────┴──────┴──────┴─────────┘  │
│  Option 1: Safe Option                           │
│  [One short sentence]                            │
│     ⬡        🖌       ◇                          │
│  Wireframe  Clean  Polished                      │
│  ┌──────────────────────────────────────────┐    │
│  │  ● ● ●              yourapp.com  ─ □ ✕  │    │
│  │  ┌────────────────────────────────────┐  │    │
│  │  │                                    │  │    │
│  │  │  [Content for selected sub-tab]    │  │    │
│  │  │                                    │  │    │
│  │  └────────────────────────────────────┘  │    │
│  └──────────────────────────────────────────┘    │
└─────────────────────────────────────────────────┘
```

Each main tab shows its own option panel with sub-tabs. Summary tab shows scoring table + recommendation. Only one panel visible at a time.

**Attribution footer** — Phase 1 must include a footer at the bottom of the page (outside `page-wrapper`, after all option panels):

```html
<footer class="skill-footer">
  This design was crafted by the <a href="https://github.com/Magdoub/claude-wireframe-skill">Wireframe skill</a> — follow on GitHub
</footer>
```

Styled in `styles.css`: centered, `font-size: 11px`, `color: #999`, `padding: 16px`, `margin-top: 8px`. Link color `#666`, underline on hover only (`text-decoration: none` default, `text-decoration: underline` on `:hover`).

#### Browser / Device Frame

Each option's sub-tab content (Wireframe, Clean, Polished) MUST be wrapped in a simulated **browser window** frame. This makes the output feel like a contained design artifact, not a live webpage.

**Browser frame rules:**
- **Max width**: `max-width: 900px; margin: 0 auto;` — designs must NOT stretch to fill the full viewport on wide screens.
- **Chrome bar**: Light gray bar (`#f0f0f0` for wireframe, colored for variants) with 3 dots (gray circles in wireframe, red/yellow/green in color variants) and a URL-like text (e.g., `yourapp.com/feature-name`).
- **Content area**: `overflow: hidden` — no content may visually escape the browser frame. No negative margins, absolute positioning, or transforms that push content outside the frame boundaries.
- **Mobile frame**: If `design-context.md` target platform is **Mobile**, use a phone frame instead (rounded rect with notch/status bar, `max-width: 390px`).
- Frame is purely decorative CSS — no extra dependencies.

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

**Options 2–5** — pick 4 from: Progressive Disclosure, Dashboard-First, Wizard/Step-by-Step, Hub-and-Spoke, Inline Editing, Split View, Card-Based, Conversational, Kanban/Column, Timeline, Search-First, Contextual Actions, Feed-Based, Spatial/Map-Centric, Gesture-Driven, Command Palette, Notification-Driven, Floating Action, Comparison Table, Drag-and-Drop, Accordion/Collapsible, Gamified Progress — or create your own.

#### Content Guidelines
- Realistic placeholder content (not lorem ipsum), realistic data quantities
- Show empty/loading/error states where relevant
- Label interactive elements clearly

#### Annotation System
Small numbered markers (①②③) on wireframe elements, corresponding to UX notes below. Visible but not dominant — small circles with light border. **Wireframe sub-tab only** — not on color variants.

#### Sub-tab Behavior

**Sub-tabs** — iOS-style icon tab bar (3 items: Wireframe, Clean, Polished):

| Tab | Icon | No content yet | Ready | Hover (ready) | Active (selected) |
|---|---|---|---|---|---|
| Wireframe | `sketch` | — | — | — | `#000` + 2px bottom border |
| Clean | `paint` | `#999` (gray) | `#64B5F6` (light blue) | `#1E88E5` (medium blue) | `#1565C0` (dark blue) + 2px bottom border |
| Polished | `diamond-one` | `#999` (gray) | `#81C784` (light green) | `#43A047` (medium green) | `#2E7D32` (dark green) + 2px bottom border |

Wireframe icon: always `#000` when active, `#666` otherwise. No progress states needed.

- 24×24 SVG icon above 12px label (system font), 2px colored underline when active
- Sub-tab bar visually lighter than main tabs — compact spacing for clear hierarchy.
- Clean and Polished sub-tabs start gray (`#999`) and transition to their active colors when CSS loads.

**"In progress" badge on Clean/Polished sub-tabs:**

Phase 1 must add a small badge element to each Clean and Polished sub-tab button:

```html
<span class="sub-tab-badge">generating...</span>
```

Styled as: small pill badge (`font-size: 9px; background: #f0f0f0; color: #999; border-radius: 8px; padding: 1px 6px;`) positioned above or next to the icon.

**CSS self-reveal mechanism (replaces polling):**

Phase 1 creates empty CSS files and links them in `<head>`:

```html
<link rel="stylesheet" href="styles-opt1.css">
<link rel="stylesheet" href="styles-opt2.css">
<link rel="stylesheet" href="styles-opt3.css">
<link rel="stylesheet" href="styles-opt4.css">
<link rel="stylesheet" href="styles-opt5.css">
```

Instead of JS polling to detect when CSS files have content, **the variant CSS files themselves** contain rules that handle UX transitions. When a Phase 2 agent writes `styles-optN.css`, that CSS includes self-reveal rules at the end that hide badges and color sub-tabs for all states (ready, hover, active). No JS detection needed — pure CSS cascade.

How it works:
- When the CSS file is empty → badges visible, sub-tabs gray (Phase 1 defaults)
- When the CSS file has content → its own rules hide the badges and color the tabs in all states: ready (light color), hover (medium color), and active (dark color + border)
- No `pollVariantCSS`, no `optionReady`, no `readyOptions` tracking — zero JS detection

Phase 2 agents must include these rules at the end of each `styles-optN.css`:

```css
/* --- Self-reveal: hide badge, color sub-tabs --- */
#frame-optN-tabs .sub-tab-badge { display: none; }
#frame-optN-tabs .sub-tab-btn[data-variant="clean"] { color: #64B5F6; }
#frame-optN-tabs .sub-tab-btn[data-variant="clean"]:hover { color: #1E88E5; }
#frame-optN-tabs .sub-tab-btn[data-variant="clean"].active { color: #1565C0; border-bottom-color: #1565C0; }
#frame-optN-tabs .sub-tab-btn[data-variant="polished"] { color: #81C784; }
#frame-optN-tabs .sub-tab-btn[data-variant="polished"]:hover { color: #43A047; }
#frame-optN-tabs .sub-tab-btn[data-variant="polished"].active { color: #2E7D32; border-bottom-color: #2E7D32; }
```

**Completion banner:**

Phase 1 includes a hidden banner div at the top of the page:

```html
<div class="completion-banner" id="completion-banner" style="display:none;">
  ✦ Visual designs complete — all options now have Clean + Polished variants
  <button onclick="this.parentElement.style.display='none'" style="float:right;background:none;border:none;cursor:pointer;font-size:16px;">×</button>
</div>
```

Banner styling: full-width, warm amber background (`#ffe5c0`), centered text, `font-size: 13px`, `padding: 10px`, dismissible with × button. Auto-hides after 8 seconds via `setTimeout`. Entrance animation: `animation: bannerSlideDown 0.4s ease-out`. Phase 1 must include this keyframe in `styles.css`:

```css
@keyframes bannerSlideDown {
  from { transform: translateY(-100%); opacity: 0; }
  to { transform: translateY(0); opacity: 1; }
}
```

**One-time load check for completion banner** — Phase 1's inline `<script>` includes:

```js
window.addEventListener('load', function() {
  var badges = document.querySelectorAll('.sub-tab-badge');
  var anyVisible = false;
  badges.forEach(function(b) {
    if (getComputedStyle(b).display !== 'none') anyVisible = true;
  });
  if (!anyVisible && badges.length > 0) {
    var banner = document.getElementById('completion-banner');
    if (banner) {
      banner.style.display = 'block';
      setTimeout(function() { banner.style.display = 'none'; }, 8000);
    }
  }
});
```

This runs once on page load. When the main agent re-opens the page after all 5 agents complete, the CSS self-reveal rules hide all badges, the load check sees none visible → shows banner. No polling needed.

**Sub-tab JS behavior:**

The inline script only needs:
- Main tab switching
- Sub-tab switching (class toggle on `#frame-optN` between `wireframe`, `clean`, `polished`)
- One-time load check for completion banner (above)
- NO `pollVariantCSS`, NO `optionReady`, NO `readyOptions` tracking

Sub-tab clicks swap the class on `#frame-optN` between `wireframe`, `clean`, `polished`. No separate content panels — same HTML, different CSS class.

### 3e. Launch Parallel Color Agents (Phase 2)

Immediately after writing `index.html` and `styles.css`, launch **5 parallel foreground Task agents** in a single tool-call message — one per option. Each agent writes its CSS to a **separate file** (`styles-opt1.css`, `styles-opt2.css`, `styles-opt3.css`, `styles-opt4.css`, `styles-opt5.css`). The Phase 1 HTML must include `<link rel="stylesheet" href="styles-optN.css">` tags for all 5 variant CSS files in `<head>` (empty files created during Phase 1).

```
Launch all 5 Task agents in ONE tool-call message (parallel):

Agent 1: description: "[Option 1 Name]: Visual Designer"
Agent 2: description: "[Option 2 Name]: Visual Designer"
Agent 3: description: "[Option 3 Name]: Visual Designer"
Agent 4: description: "[Option 4 Name]: Visual Designer"
Agent 5: description: "[Option 5 Name]: Visual Designer"

All with:
  subagent_type: "general-purpose"
  run_in_background: false
```

Each agent's prompt MUST include:
1. **File paths**: Full absolute paths to `index.html`, the agent's own `styles-optN.css`, `design-taste.md`, and `design-context.md`
2. **Visual Designer persona**: The instructions below
3. **Scope**: "Write all CSS to `styles-optN.css`. Do NOT modify `index.html`."
4. **CSS budget**: ≤ 200 lines in `styles-optN.css`

**Visual Designer persona for each agent prompt:**

> You are the Visual Designer for Option N: [Option Name]. The UX Architect's B&W wireframe layout is locked. Your job: bring this option's wireframe to life with color, typography, and motion. Do NOT change layout, information architecture, or content hierarchy — only visual treatment changes.
>
> **Your task:**
> 1. Read `index.html`, `styles-optN.css`, `design-taste.md`, and `design-context.md`
> 2. Study the wireframe HTML structure for Option N (inside `#frame-optN`)
> 3. Write CSS to `styles-optN.css` with:
>    a. `.clean .selector` rules for the Clean variant
>    b. `.polished .selector` rules for the Polished variant
>    c. Browser chrome coloring: `.clean .browser-dots span:nth-child(1) { background: #ff5f57; }` etc.
>    d. Use `::before` / `::after` pseudo-elements for decorative accents in Polished (badges, overlays, accent borders)
> 4. Do NOT modify `index.html` — CSS is your only output
> 5. Google Fonts allowed via `@import` at top of `styles-optN.css`
> 6. Budget: ≤ 200 lines
> 7. At the END of your CSS file, include these self-reveal rules (replace N with your option number):
>    `#frame-optN-tabs .sub-tab-badge { display: none; }`
>    `#frame-optN-tabs .sub-tab-btn[data-variant="clean"] { color: #64B5F6; }`
>    `#frame-optN-tabs .sub-tab-btn[data-variant="clean"]:hover { color: #1E88E5; }`
>    `#frame-optN-tabs .sub-tab-btn[data-variant="clean"].active { color: #1565C0; border-bottom-color: #1565C0; }`
>    `#frame-optN-tabs .sub-tab-btn[data-variant="polished"] { color: #81C784; }`
>    `#frame-optN-tabs .sub-tab-btn[data-variant="polished"]:hover { color: #43A047; }`
>    `#frame-optN-tabs .sub-tab-btn[data-variant="polished"].active { color: #2E7D32; border-bottom-color: #2E7D32; }`
>
> Do NOT duplicate layout rules — only override: `color`, `background`, `border-color`, `box-shadow`, `font-family`, `font-weight`, `transition`, `animation`.
>
> **CSS Anti-Patterns — MUST avoid:**
>
> 1. **Decorative borders overlapping content**: Never add `border-top`, `border-left`, or decorative lines to a container without ensuring adequate `padding` or `margin` on the same side. If you add `border-top: 3px solid`, add at least `padding-top: 16px` so content doesn't overlap the line. Test: would any child element touch or overlap the border?
> 2. **Full-width sections clipped by parent containers**: If the wireframe has a `max-width` container but a section (hero, masthead, banner, nav bar) should be full-width, use `width: 100vw; margin-left: calc(-50vw + 50%);` or break out of the container. Never leave a full-width section visually cut off at the sides. Test: does any section appear to "float" with gaps on the left/right edges?
> 3. **Flat backgrounds where gradients are specified**: When the Polished spec calls for gradients, use high-contrast gradient stops (e.g., `linear-gradient(135deg, #e8f5e9 0%, #c8e6c9 50%, #a5d6a7 100%)`) — not near-identical colors that appear flat. The gradient must be perceptible at a glance. Test: screenshot it — can you see the gradient without squinting?
> 4. **Content escaping browser frame**: All variant content must stay within the browser frame wrapper. Use `overflow: hidden` on the frame content area. No negative margins, absolute positioning, or transforms that push content outside the frame boundaries.
> 5. **Dark mode in Polished**: NEVER use dark backgrounds (`#0a-#2f` range) for Polished. Polished must keep the same light base as Clean. Dark backgrounds make it look like a different theme, not a polished version.
>
> **Clean (Style A)**: Simple, clean colors from `design-context.md` palette (or Warmth/Precision tokens from `design-taste.md`). System fonts only (`-apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif`). Solid fills, clean typography, proper spacing. No gradients, no shadows, no effects — just color applied to the layout.
>
> **Polished (Style B)**: Builds on Clean's color scheme — same backgrounds, same palette, same light feel. The difference is **craft and finish**, not a different color scheme. Specifically:
> - **Typography**: Use a Google Font pairing (display + body) that complements Clean's system fonts. Slightly more refined sizing/spacing.
> - **Gradients**: Replace flat solid backgrounds on heroes/banners/card headers with **subtle gradients** using lighter/darker shades of the same Clean colors (e.g., `#f0faf5` → `#e2f5ec`, not dark backgrounds).
> - **Depth & shadows**: Elevated cards with soft multi-layer shadows, subtle background glow effects. All on light backgrounds.
> - **Animation**: Staggered entrance reveals, hover scale/lift on cards and buttons, micro-interactions on form inputs (focus glow), smooth transitions. Respect `prefers-reduced-motion`.
> - **Decorative accents**: `::before`/`::after` pseudo-elements for colored top borders on cards, pill badges, accent lines, subtle background patterns.
> - **Hover effects**: Interactive lift, color shift, underline animations on links and buttons.
>
> **CRITICAL: Polished must NOT use dark backgrounds.** Keep the same light/white base as Clean. If Clean has a white hero, Polished has a white-to-light-tint gradient hero — NOT a dark one. The goal is "Clean but with more craft" — not a different theme.
>
> Polished MUST use the color palette from `design-context.md` as its foundation — do not invent new brand colors. Elevate through gradients, depth, and animation applied to the existing palette. If Clean is "color applied to wireframe", Polished is "Clean but with more craft and finish".
>
> **Rules:**
> - EXACT same layout/structure as B&W wireframe — only visual treatment changes
> - Each variant must have a distinct, intentional aesthetic
> - Apply quality checks from `design-taste.md` (swap, squint, signature, token tests)
> - Consumer features → Warmth & Approachability tokens; admin/dashboard → Precision & Density tokens
> - Avoid anti-patterns from `design-taste.md`
> - Both Clean and Polished MUST use colors from `design-context.md` palette — no invented brand colors
> - No annotation markers on color variants — annotations are hidden via CSS class toggle

As each parallel agent returns, the main agent reports to the user:
> "✔ [Option Name] — Clean + Polished ready."

After all 5 return, confirm everything is done.

### 3f. Report to User (two phases)

**3f-i. After wireframes written (before launching color agents):**

Open the generated HTML file: `open wireframe/DDMM-<feature-name>/index.html`

Then tell the user:
- How many options were generated (1 safe + 4 exploratory)
- Which option is recommended and why (1 sentence)
- Brief summary of each option's UX approach
- If an optimization goal was provided, highlight which option(s) best serve that goal
- Print the folder path `wireframe/DDMM-<feature-name>/`

**Then decide whether to launch Phase 2 (using the scope stored in Step 3b-ii):**

- If scope is **wireframes+visuals**: add the note **"Color variants (Clean + Polished) are generating now — I'll update you as each option completes."** and proceed directly to Step 3e.
- If scope is **wireframes-only**: skip Phase 2 entirely — wireframes are the final output.

**3f-ii. As each parallel agent returns:**

Report progress as each of the 5 agents completes:
> "✔ [Option Name] — Clean + Polished ready."

**3f-iii. After all 5 agents return:**

Re-open the HTML file so the user sees the final result:

```
open wireframe/DDMM-<feature-name>/index.html
```

Tell the user: **"All 5 options now have color variants (Clean + Polished). The page has been re-opened with the final result."**

Note: Do not include "Refresh your browser" language — the CSS self-reveal mechanism and completion banner handle notification automatically when the page is re-opened.

## Step 4: Update Design Context

After generating wireframes, check if the feature reveals new patterns or page types not yet documented in `wireframe/brain/design-context.md`. If so, append the new information to the appropriate section.

## Rules

- **Persona 1 (UX Architect)** owns wireframe structure: layout, content hierarchy, navigation, interactive behavior.
- **Persona 2 (Visual Designer)** owns color variants: color, typography, spacing refinement, motion. Layout is locked.
- Option 1 must use existing patterns from `design-context.md`. Options 2–5 must each represent a genuinely different UX philosophy.
- Wireframes must be functional HTML. B&W constraint strict for Wireframe sub-tab.
- Read both `design-context.md` and `design-taste.md` every time.
- When wireframing a component (not a full page), render it as a standalone element filling the available width.
