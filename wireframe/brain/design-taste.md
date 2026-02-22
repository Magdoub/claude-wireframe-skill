# Design Taste Reference

This document defines the aesthetic principles, style tokens, and quality standards for colorful UI variants (Clean, Polished). Read this alongside `design-context.md`.

---

## Craft Foundations

### Surface & Elevation
- Use subtle layering to create depth — not drop shadows on everything, but intentional elevation where it communicates hierarchy
- Background surfaces should have barely-perceptible tonal differences (e.g., `#fafafa` vs `#ffffff`) to separate zones
- Cards and elevated elements: prefer soft shadows (`0 1px 3px rgba(0,0,0,0.08)`) over hard borders where possible
- Reserve strong elevation (larger shadows) for elements that genuinely float: modals, dropdowns, tooltips

### Borders & Dividers
- Borders are a last resort for separation — prefer spacing, background contrast, or subtle shadows first
- When borders are needed: 1px, low-contrast (e.g., `rgba(0,0,0,0.06)` not `#cccccc`)
- Never stack borders — if a card has a border, don't also put a border on its container
- Dividers between list items: use `border-bottom` on items, not `border-top` — avoids double-line at edges

### Spacing System
- Use a consistent scale: 4px base unit — 4, 8, 12, 16, 24, 32, 48, 64, 96
- Generous padding inside containers (min 16px, prefer 24px)
- Group related elements tightly (8px gap), separate unrelated groups with more space (24px+)
- Whitespace is a feature, not wasted space — let content breathe

### Color Intent
- Every color must have a job: primary action, success feedback, warning, error, or brand accent
- Avoid decorative color — if it doesn't communicate something, it shouldn't be colored
- Use color sparingly so it retains meaning. A page with 6 colors everywhere has zero color hierarchy
- Tint backgrounds with brand color at very low opacity (2-5%) for subtle warmth without distraction

### Typography
- Limit to 2 font weights in most UIs: regular (400) for body, semibold (600) for headings/emphasis
- Size scale: 12px (captions) / 14px (body) / 16px (large body) / 20px (h3) / 24px (h2) / 32px (h1)
- Line-height: 1.5 for body text, 1.2-1.3 for headings
- Letter-spacing: slight positive tracking on all-caps labels (+0.5px), default elsewhere

### Animation & Transitions
- Default transition: `150ms ease-out` for hover states, `200ms ease-out` for layout changes
- Entrance animations: fade + slight translate (8-12px), staggered by 50ms between siblings
- Never animate purely for decoration — motion should communicate state change or guide attention
- Respect `prefers-reduced-motion`: wrap animations in `@media (prefers-reduced-motion: no-preference)`

### Interactive States
- Every clickable element needs: default, hover, active (pressed), focus-visible, disabled
- Hover: subtle background shift or shadow increase — not color change
- Active/pressed: slight scale-down (`transform: scale(0.98)`) or darker background
- Focus-visible: 2px offset outline in brand color — never remove focus indicators
- Disabled: 40% opacity, `cursor: not-allowed`, `pointer-events: none`

---

## Style Tokens: Warmth & Approachability (Consumer Apps)

Use these tokens for consumer-facing features — things end-users interact with directly.

| Token | Value |
|-------|-------|
| **Palette** | Warm stone: `#1a1a1a` (text), `#6b5b4f` (secondary), `#d4a574` (accent), `#f5f0eb` (surface), `#faf8f5` (background) |
| **Spacing** | Generous — 24px container padding, 16px element gaps, 48px section gaps |
| **Border radius** | Soft — 8px for cards, 6px for buttons, 12px for modals |
| **Shadows** | Soft and warm — `0 2px 8px rgba(107,91,79,0.08)` |
| **Typography** | Inter or system sans-serif, 16px base, generous line-height (1.6) |
| **Buttons** | Filled primary with rounded corners, ghost secondary, subtle hover lift |
| **Icons** | Rounded stroke style, 20px default size |

---

## Style Tokens: Precision & Density (Admin / Dashboard)

Use these tokens for admin panels, dashboards, settings pages, and internal tools.

| Token | Value |
|-------|-------|
| **Palette** | Cool slate: `#0f172a` (text), `#475569` (secondary), `#3b82f6` (accent), `#f1f5f9` (surface), `#f8fafc` (background) |
| **Spacing** | Compact — 16px container padding, 8px element gaps, 24px section gaps |
| **Border radius** | Sharp — 4px for cards, 4px for buttons, 8px for modals |
| **Shadows** | Minimal — prefer 1px borders (`#e2e8f0`) over shadows |
| **Typography** | System fonts (`-apple-system, ...`), 14px base, tight line-height (1.4) |
| **Buttons** | Compact with clear borders, icon+label combos, keyboard shortcut hints |
| **Data density** | Tables with 32px row height, inline status badges, compact form fields |

---

## Quality Checks

Apply mentally before outputting any color variant:

- **Swap Test**: Cover the brand — if it could be any app, it lacks personality.
- **Squint Test**: Blur your eyes — you should still see primary action, navigation, main content, and hierarchy.
- **Signature Test**: At least one distinctive element — unique pattern, unexpected color, thoughtful micro-detail.
- **Token Test**: Variant consistently uses its chosen token set — no mixing warm palette with sharp borders.

---

## Anti-Patterns: Generic AI Aesthetics

Avoid these patterns that make UIs feel AI-generated and templated:

- **Inter + purple gradient on white** — the default AI look. If using Inter, pair it with a non-obvious palette
- **Gratuitous glassmorphism** — frosted glass everywhere with no purpose
- **Rainbow gradients as accents** — gradient badges, gradient borders, gradient everything
- **Identical card grids** — 3 cards in a row with icon, heading, description, all same height
- **Oversized hero with centered text** — giant padding, centered headline, centered subhead, centered CTA
- **Blue primary button with gray secondary** — fine functionally, but boring as a design choice
- **Decorative blobs and abstract shapes** — background blobs that add nothing
- **Excessive border-radius** (pill-shaped everything) — makes UI feel toylike
- **Low-contrast pastel text** — looks "soft" but fails accessibility
- **Shadows that don't match light source** — multiple shadow directions on the same page

---

## Key Principle

Every variant needs a clear aesthetic direction — bold, minimal, warm, cool, brutalist, playful all work. **Generic doesn't.** Each of the 2 color variants (Clean, Polished) must feel like a deliberate design decision, not a random theme swap. The Wireframe proves structure; the color variants prove it can work *emotionally*.
