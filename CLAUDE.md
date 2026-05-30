# CLAUDE.md — JARVIS Command Center

## Project Overview

A single-file static web dashboard styled as a sci-fi "JARVIS Command Center." Built with vanilla HTML/CSS/JavaScript — no build system, no framework, no npm.

The UI is written in Korean (한국어) with English labels for status indicators and technical tags.

## File Structure

```
test_20260525/
├── index.html      # The entire application (HTML + CSS + JS in one file)
└── .gitignore      # Covers macOS, editors, OS artifacts, logs, node_modules
```

There is no `package.json`, no bundler, no test runner, and no server. Open `index.html` directly in a browser.

## Architecture

Everything lives in `index.html`, organized in three `<style>`, markup, and `<script>` sections:

### CSS (lines 8–704)
- CSS custom properties (`:root`) define the color palette and glow effects
- All layout uses CSS Grid and Flexbox
- Responsive breakpoints at 1100px (2-column) and 720px (1-column)
- Animations: `scanlines`, `spin-slow`, `pulse`, `shimmer`, `blink`
- `.panel` uses `clip-path` for the angled-corner aesthetic

### HTML Structure (lines 706–1005)
| Section | Description |
|---|---|
| `<header>` | Logo, live clock, CPU/MEM/NET status widgets |
| `.main-grid` (3 columns) | Todo panel, Schedule panel, Projects + Timeline |
| `.warning-section` | Critical/warning alert cards at the bottom |

### JavaScript (lines 1006–1112)
| Function/Block | Purpose |
|---|---|
| `updateClock()` | Updates `#clock` and `#date` every second |
| `animateWidgets()` | Randomizes CPU/MEM/NET bar values every 2s |
| `loadTodos()` / `saveTodos()` | `localStorage` persistence under key `jarvis_todos` |
| `renderTodos()` | Rebuilds the todo list DOM from state |
| `toggleTodo(id)` | Flips `done` on a todo item |
| `deleteTodo(e, id)` | Removes a todo (stops click propagation) |
| `addTodo()` | Appends a new todo; also bound to `Enter` key |
| `escHtml(s)` | XSS-safe HTML escaping for user todo text |

## Color Palette

| Variable | Value | Usage |
|---|---|---|
| `--cyan` | `#00f5ff` | Primary UI color, borders, text |
| `--blue` | `#0080ff` | Schedule times, secondary accents |
| `--orange` | `#ff6b00` | Warning cards, project delay tags |
| `--red` | `#ff2244` | Critical cards, critical project tags |
| `--green` | `#00ff88` | Completed tags, "NOW" timeline line |
| `--bg` | `#020812` | Page background |
| `--panel` | `rgba(0,20,50,0.7)` | Panel background |

## Key Conventions

- **No external JS** — all logic is inline vanilla JS.
- **Google Fonts** are the only external dependency (`Orbitron`, `Rajdhani`).
- **XSS protection**: all user-supplied text rendered via `escHtml()` before `innerHTML`.
- **localStorage key**: `jarvis_todos` stores the todo array as JSON.
- **Todo IDs**: assigned as `max(existing ids) + 1`; not sequential after deletions.
- **Priority values**: `"high"` / `"mid"` / `"low"` — used both as data and as CSS class suffixes (`priority-high`, etc.).
- **Static data**: schedule items, project progress bars, timeline events, and warning cards are all hardcoded in HTML. Only todos are dynamic.

## Development Workflow

Since there is no build step, the workflow is:

1. Edit `index.html` directly.
2. Open (or refresh) `index.html` in a browser to verify.
3. Commit and push to the feature branch.

```bash
# Switch to / create the feature branch
git checkout claude/claude-md-docs-DT1lx

# After edits, stage and commit
git add index.html
git commit -m "describe what changed"

# Push
git push -u origin claude/claude-md-docs-DT1lx
```

**Active branch**: `claude/claude-md-docs-DT1lx`  
**Main branch**: `main`

## Making Changes

### Adding a new panel
1. Add a `<div class="panel">` block inside `.main-grid`.
2. Use `.panel-header` + a content wrapper following the existing pattern.
3. The 3-column grid auto-places panels; adjust `grid-template-columns` or use `grid-column: span 2` as needed.

### Adding a schedule item
Copy an existing `.schedule-item` block inside `#schedule-list`. Use one of the dot color classes: `dot-cyan`, `dot-blue`, `dot-orange`, `dot-green`.

### Adding a warning card
Copy an existing `.warn-card` inside `.warning-grid`. Add class `critical` for the red variant. Update `#warn-count` text at the bottom of the script if needed.

### Modifying project progress
Update the `style="width:XX%"` on `.progress-fill` and the `<span class="project-pct">` text to match.

### Modifying default todos
Edit the `DEFAULT_TODOS` array (around line 1037). This only applies when `localStorage` has no saved state.

## What NOT to Do

- Do not introduce a build system or package manager unless the project scope changes significantly.
- Do not use `innerHTML` with unsanitized user input — always pass user text through `escHtml()`.
- Do not move CSS to a separate file without a reason; the single-file approach is intentional for this project.
