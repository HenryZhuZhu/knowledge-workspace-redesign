# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A design-iteration project: redesigning the homepage of an enterprise "knowledge workbench" (知识工作台 — knowledge base + project collaboration + tasks + AI copilot). The original UI being redesigned is the screenshot ` index.JPG` (note the leading space in the filename). The deliverable is a self-contained interactive prototype, not a production app.

## Running

No build, lint, or test tooling — there is no `package.json`. The prototype is a single static file:

```bash
open index.html        # macOS — opens the demo in the default browser
```

Edits to `index.html` are seen by reloading the browser.

## Architecture

Everything lives in `index.html` (~500 lines): one file with embedded `<style>` and `<script>`, no dependencies, no framework, no external assets (icons are inline SVG, covers are emoji). Treat this single-file constraint as intentional — keep it self-contained.

The file has three layers, in order:

1. **Design tokens** — the `:root` CSS variable block is the single source of truth: one brand color ramp + neutral gray ramp + semantic colors (used *only* for status). Type scale, radii, shadows, spacing, and layout widths are all tokens. `body.dark` overrides tokens for the dark theme. When changing visuals, edit tokens rather than hardcoding values.
2. **Layout** — a 3-column CSS grid (`.app`): left nav / main / right panel. Collapsing nav or hiding the right panel is done by toggling classes on `.app` that swap the `grid-template-columns` (see `--nav-w`, `--nav-w-collapsed`, `--right-w`).
3. **Data + render + interaction** — in the `<script>`: `DOCS` and `TASKS` are plain-array mock data; pure render helpers (`docCard`, `renderResume`, `renderPinned`, `renderTasks`) build HTML strings; interaction handlers re-render on state change. State is a few module-level globals (`curTab`, `curTag`, `curSearch`, `pinned`).

### Conventions that matter

- **Data-driven cards.** To add/change content, edit the `DOCS` array (and `TASKS`). Each doc carries `mine` / `fav` / `edited` / `ts` (sort weight) / `ci` (cover-color index into `COVERS`). Render functions derive everything from these fields; don't hand-write card markup.
- **Tabs are declarative.** `TAB_META` maps each tab to its title + a `base()` filter/sort. Adding a tab = one entry in `TAB_META` + one `.tab` button with a matching `data-tab`.
- **Persistence is localStorage**, keyed `kw_*` (`kw_tab` = last active tab, `kw_pins` = pinned doc ids). The default tab is "最近编辑" (recent edits), restored from `kw_tab` on load.
- Semantic colors (`--success/warning/danger/info`) are reserved for status badges; the brand color is for actions/identity. Don't use semantic colors decoratively.

## Design intent (drives changes — read before redesigning)

This is a knowledge/productivity tool, so the homepage's core job is **resuming work**, not discovery. Key principles the current design encodes, per `README.md`:
- Default content = the user's own recent/frequent work ("继续编辑" resume row + "置顶·常用" pinned section), *not* a recommendation feed.
- Pinned items hold a **fixed position** on purpose — spatial stability lets users find things by recognition rather than recall.
- "推荐" (recommend) is demoted to an opt-in last tab.

Preserve these unless a change is explicitly meant to revisit them.

## Iteration workflow

Versions are tracked in git and pushed to GitHub (remote `origin`, branch `main`). Each design iteration is a commit prefixed `vN:` with rationale in the body; `README.md` carries a matching "vN" section explaining the *why* (often grounded in user-research findings). When delivering a new iteration, update both the demo and the README's version log, then commit + push.
