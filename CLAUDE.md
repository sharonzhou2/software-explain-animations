# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Static site of interactive animated explanations of software engineering concepts. No build system, no bundler, no package manager — each animation is a single self-contained HTML file.

## Development

Serve locally with any static file server from the repo root:

```bash
python3 -m http.server 8000
# or
npx serve .
```

No build step, no tests, no linting.

## Architecture

- **`index.html`** — Landing page listing all animations as card links
- **`<topic>/index.html`** — Each animation is one self-contained HTML file (inline CSS + inline JS)
- **`garbage-collection/`** — Empty directory, placeholder for next animation

### Per-Animation File Structure

Each animation HTML file follows the same pattern:

1. **Head**: Inline `<style>` block with all CSS (no external stylesheets)
2. **Body**: A `#root` div and a `<nav class="topnav">` breadcrumb
3. **CDN scripts**: React 18 (UMD), ReactDOM 18, Framer Motion 11 — loaded from unpkg/jsdelivr
4. **Inline `<script>`**: All React components and animation logic written with `React.createElement` (aliased as `e`), no JSX, no transpiler

### Shared Conventions

- **Theme object `T`**: Color palette constants (bg, surface, border, text, accent colors) — consistent dark theme across animations
- **Font**: Inter from Google Fonts is the primary sans — `'Inter', system-ui, -apple-system, sans-serif`. `'SF Mono', monospace` for code
- **Animation helpers**: `sp(stiffness, damping)` for spring configs, `ez` for easing curves
- **Motion aliases**: `M = motion`, `AP = AnimatePresence` from Framer Motion
- **Scene-based structure**: Each animation defines a `SCENES` array of objects with `id`, `title`, `sub` (subtitle), `code` (tokenized code lines), and scene-specific data
- **Token-based code display**: Code is represented as arrays of `[type, text]` tuples (e.g., `["fn","console"]`, `["punc","."]`) with syntax-highlighting styles in `TOKEN_STYLES` / `TS`
- **Two-panel layout**: Left side shows code + controls, right side shows animated visualization with a detail sidebar

### Icons — NEVER Use Emojis

**Rule**: Never use emoji characters or Unicode symbols (✓ ✗ ✕ ⚠ ⏸ ▶ ↺ ✦ ⤷ 🔥 ⚡ 😱 etc.) as UI icons. Always use **LineIcons v5.0**.

- **CDN**: `<link href="https://cdn.lineicons.com/5.0/lineicons.css" rel="stylesheet">` — already in every page's `<head>`
- **Usage in React**: `e("i", { className: "lni lni-<icon-name>" })`
- **Common icons used in this project**:
  - `lni-xmark` — close / cross / no
  - `lni-check` — check / yes / ok
  - `lni-play`, `lni-pause`, `lni-refresh-circle-1-clockwise` — timeline controls (Play / Pause / Replay)
  - `lni-search-1`, `lni-arrow-left`, `lni-arrow-right` — navigation
  - `lni-bolt-3` — energy / fast / alert
  - `lni-info`, `lni-alarm-1` — warnings / pitfalls
  - `lni-stopwatch`, `lni-hourglass` — timing / slow
  - `lni-star-fat` — rule of thumb / highlight
  - `lni-layers-1`, `lni-gears-3`, `lni-monitor`, `lni-monitor-code`, `lni-docker`, `lni-box-closed`, `lni-rocket-5` — architecture diagrams
- **Code comments in tokenized code** (`["cmt", "..."]`): never put ✓ / ✗ inside the displayed code string — use plain text markers like `[ok]` / `[no]` instead, since code tokens render as literal text and can't host an `<i>` element
- **Inside SVG**: LineIcons don't work in SVG contexts — use a plain `<circle>`, `<path>`, or `<text>` with a simple character (e.g. `"!"`) instead
- **Finale scene act label**: use text like `"DONE"`, not `"✓"`
- **Before picking an icon**, verify it exists in v5.0 by checking `https://cdn.lineicons.com/5.0/lineicons.css`. Not every name follows the pattern you expect (e.g. `lni-search-1` exists but `lni-search-alt` does not)

### Adding a New Animation

1. Create `<topic>/index.html` following the existing pattern (copy event-loop or this-keyword as template)
2. Define `SCENES` array with scene data
3. Add a card link in the root `index.html`
