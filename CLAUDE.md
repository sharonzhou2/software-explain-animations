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
- **Animation helpers**: `sp(stiffness, damping)` for spring configs, `ez` for easing curves
- **Motion aliases**: `M = motion`, `AP = AnimatePresence` from Framer Motion
- **Scene-based structure**: Each animation defines a `SCENES` array of objects with `id`, `title`, `sub` (subtitle), `code` (tokenized code lines), and scene-specific data
- **Token-based code display**: Code is represented as arrays of `[type, text]` tuples (e.g., `["fn","console"]`, `["punc","."]`) with syntax-highlighting styles in `TOKEN_STYLES` / `TS`
- **Two-panel layout**: Left side shows code + controls, right side shows animated visualization with a detail sidebar

### Adding a New Animation

1. Create `<topic>/index.html` following the existing pattern (copy event-loop or this-keyword as template)
2. Define `SCENES` array with scene data
3. Add a card link in the root `index.html`
