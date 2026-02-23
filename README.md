# claude-wireframe-skill

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill that generates 5 UX prototypes as B&W wireframes, then adds Clean and Polished color variants via parallel agents.


## Installation
Open up the terminal and run this command:
```bash
mkdir -p ~/.claude/skills
git clone https://github.com/Magdoub/claude-wireframe-skill.git ~/.claude/skills/wireframe
```

Or manually: download [`SKILL.md`](SKILL.md) and place it at `~/.claude/skills/wireframe/SKILL.md`.

### Update

```bash
git -C ~/.claude/skills/wireframe pull
```

## What it does

- Generates 5 UX approaches (1 safe extension of your design system + 4 exploratory)
- B&W wireframes appear first (~40-60s), then 5 parallel agents add color variants
- Each option has 3 sub-tabs: Wireframe (B&W) / Clean (flat color) / Polished (gradients, shadows, animation)
- Prototypes are interactive: working tabs, accordions, hover states, all inside a browser-frame mockup
- On first run, scans your CSS, JS, templates + screenshots to build persistent design context
- Extracts optimization goals from your prompt ("more conversions", "reduce clutter", etc.)
- Includes a summary tab with scoring table and a recommended option

## How it works

1. **Phase 1** (~40-60s): generates 5 B&W wireframes in a single HTML file, opens it in your browser
2. **Phase 2** (parallel): 5 agents each write CSS color variants (Clean + Polished) for their option
3. Sub-tabs turn colorful as each option's variants finish, no page reload needed

<div align="center">
  <video src="https://github.com/user-attachments/assets/8cfee587-5379-4af2-97f1-03138a14fa52" width="600"></video>
</div>

After running `/wireframe improve the design of my header landing it looks crammed`

<div align="center">
  <img src="https://github.com/user-attachments/assets/5e769f34-e63c-40f2-9f99-511c438ed2b8" alt="wireframe skill v2" width="750">
</div>

## Usage

In any project with Claude Code:

```
/wireframe simplify the signup flow
/wireframe add a search bar to the listing page
/wireframe redesign the dashboard with better data density
/wireframe add an onboarding wizard for new users
```

### First run

The first time you use `/wireframe` in a project, it will:

1. Scan your codebase for CSS, JS, and template files to understand existing patterns
2. Ask for 2-3 screenshots of your current app
3. Create a `wireframe/brain/design-context.md` file with persistent design context

Subsequent runs skip this step and load the saved context.

### Output

Each run creates a folder at `wireframe/<MMDD-feature-name>/`:

```
wireframe/
  brain/
    design-context.md    # Persistent design context (auto-generated)
    design-taste.md      # Craft principles & style tokens
  2302-signup-flow/
    index.html           # Shared HTML + inline JS
    styles.css           # Wireframe CSS (B&W)
    styles-opt1.css      # Option 1 color variants (Clean + Polished)
    styles-opt2.css      # Option 2 color variants
    styles-opt3.css      # Option 3 color variants
    styles-opt4.css      # Option 4 color variants
    styles-opt5.css      # Option 5 color variants
```

Open `index.html` in any browser. The HTML is shared across all three sub-tabs; switching between Wireframe, Clean, and Polished just swaps the CSS.

## UX Approaches

The skill generates 5 approaches per feature from philosophies like:

| Approach | Description |
|----------|-------------|
| Progressive Disclosure | Start simple, reveal complexity on demand |
| Dashboard-First | Everything visible at a glance with data density |
| Wizard/Step-by-Step | Guide users through a sequential flow |
| Hub-and-Spoke | Central overview with drill-down into details |
| Split View | Side-by-side comparison or master-detail |
| Card-Based | Modular, scannable card layout |
| Conversational | Chat-like or Q&A-style interaction |
| Kanban/Column | Column-based organization |
| Timeline | Chronological or sequential presentation |
| Search-First | Search as primary navigation pattern |

## Uninstall

```bash
rm -rf ~/.claude/skills/wireframe
```

## Author

Built by [@magdoub](https://twitter.com/magdoub)

## License

[MIT](LICENSE)
