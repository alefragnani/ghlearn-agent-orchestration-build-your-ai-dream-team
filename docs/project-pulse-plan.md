# Project Pulse Dashboard — Implementation Plan

## 1. Summary

Mona's Project Pulse is a lightweight static dashboard that helps contributors see, at a glance, which projects are active, who owns them, current status, recent activity, and priority/risk. The deliverable is a small static app plus a VS Code launch configuration, built by a four-agent team (Orchestrator, Planner, Designer, Coder) coordinated through GitHub Copilot CLI.

Exact deliverable files:

- `app/index.html` — semantic dashboard markup that renders project cards from `project-data.json`.
- `app/styles.css` — polished, responsive, accessible styling with deterministic hooks (`.dashboard`, `.project-card`, status/priority variants).
- `app/project-data.json` — top-level `projects` array with `name`, `owner`, `status`, `recentActivity`, `priority`.
- `.vscode/launch.json` — strict JSON, one **Run Project Pulse Dashboard** configuration, `cwd` = `${workspaceFolder}/app`, opens `index.html` (not a directory listing).

The Designer owns UI/UX direction for `index.html` + `styles.css`. The Coder implements all four files (design-guided for HTML/CSS, and solely responsible for the data file and launch config per the Coder agent's runnable-app rule).

## 2. Ordered implementation steps

1. **Step 1 — Design direction (Designer).** Produce concrete UI/UX guidance: information architecture, card layout, status badge system, priority treatment, typography scale, spacing, color contrast, responsive breakpoints, focus/hover states, accessibility requirements, and the required CSS hook names (`.dashboard`, `.project-card`, plus status/priority modifiers). Output is written guidance the Coder can implement without further interpretation.
2. **Step 2 — Data schema and seed (Coder).** Create `app/project-data.json` with a top-level `projects` array and 4–6 realistic sample projects. Each project must include `name`, `owner`, `status`, `recentActivity`, `priority`. Enumerate the allowed values for `status` (e.g. `active`, `at-risk`, `blocked`, `paused`, `shipped`) and `priority` (e.g. `high`, `medium`, `low`) so Designer's badge styling and Coder's markup agree.
3. **Step 3 — Static markup (Coder, guided by Designer).** Implement `app/index.html`: semantic landmarks (`<header>`, `<main>`, `<section class="dashboard">`), a project-card template rendered from `project-data.json` via a small inline `<script>` using `fetch('./project-data.json')`. Include accessible attributes (`aria-label`, meaningful headings, `role` where appropriate) and deterministic classes.
4. **Step 4 — Styling (Coder, from Designer spec).** Implement `app/styles.css`: layout grid, cards, badges (status + priority variants), spacing, typography, responsive breakpoints, focus outlines, reduced-motion respect. Uses only the hooks defined in Step 1.
5. **Step 5 — Launch configuration (Coder).** Create `.vscode/launch.json` with a single **Run Project Pulse Dashboard** configuration. Strict JSON (no comments). `cwd` = `${workspaceFolder}/app`. Serve from `app/` and open `index.html` directly (not a directory listing). Use a deterministic port and URL.
6. **Step 6 — Integration verification (Orchestrator + Coder).** Load the launch config, confirm the dashboard renders with data (not a raw JSON view or directory listing), sanity-check responsiveness and accessibility hooks.

## 3. File assignments per step

| Step | File | Owner | Contributor |
|---|---|---|---|
| 1 | (no repo files — design spec is guidance only) | Designer | — |
| 2 | `app/project-data.json` | Coder | — |
| 3 | `app/index.html` | Coder | Designer (spec + review of markup semantics/classes) |
| 4 | `app/styles.css` | Coder | Designer (spec + visual review) |
| 5 | `.vscode/launch.json` | Coder | — |
| 6 | all four files (read-only verification) | Orchestrator | Coder |

Explicit ownership recap for the four required files:

- **`app/index.html`** — Coder implements; Designer owns UX/IA/accessibility direction.
- **`app/styles.css`** — Coder implements; Designer owns visual design and CSS-hook contract.
- **`app/project-data.json`** — Coder owns entirely (schema aligned with Designer's badge variants).
- **`.vscode/launch.json`** — Coder owns entirely, per the Coder agent's runnable-app rule.

## 4. Designer responsibilities

- Define information hierarchy: page header (product name + short contributor-friendly summary), dashboard grid, project card anatomy (name, owner, status badge, priority indicator, recent activity line).
- Specify the CSS hook contract: `.dashboard`, `.project-card`, plus modifier classes such as `.status--active`, `.status--at-risk`, `.status--blocked`, `.status--paused`, `.status--shipped`, `.priority--high`, `.priority--medium`, `.priority--low`.
- Provide typography scale, spacing scale, color palette with WCAG AA contrast, rounded corners, and shadow tokens.
- Specify responsive behavior (single column on narrow viewports, multi-column grid on wider viewports).
- Define accessibility requirements: semantic landmarks, heading order, focus-visible styles, non-color-only encoding of status/priority (icon or label as well), reduced-motion behavior.
- Provide review sign-off on `index.html` markup semantics and `styles.css` visual outcome.

## 5. Coder responsibilities

- Implement `app/project-data.json` with the required schema and realistic sample rows.
- Implement `app/index.html` per the Designer spec, including a minimal inline JS loader that reads `./project-data.json` and renders cards; degrade gracefully with a visible error state if the fetch fails.
- Implement `app/styles.css` per the Designer spec using exactly the agreed hook names.
- Create `.vscode/launch.json` as strict JSON, one **Run Project Pulse Dashboard** entry, `cwd = ${workspaceFolder}/app`, opens `index.html` directly (e.g. via a browser-preview / simple-browser configuration or `serve` command with a URL that ends in `/index.html`), deterministic port.
- Validate that the launch config actually renders the dashboard (not a directory listing, not raw JSON).
- Report files touched, validations performed, and residual risks.

## 6. Dependencies between steps

- Step 2 depends on Step 1 (status/priority enums must match Designer's badge variants).
- Step 3 depends on Steps 1 and 2 (markup uses Designer's hooks and Step 2's data shape).
- Step 4 depends on Step 1 (hook contract) and Step 3 (markup structure to style).
- Step 5 depends on nothing content-wise but is validated against Step 3 (must open `index.html`).
- Step 6 depends on Steps 2–5.

## 7. Work that can run in parallel

- Step 2 (`project-data.json`) and Step 5 (`.vscode/launch.json`) can be done in parallel once Step 1 has locked the status/priority enums, because their file scopes don't overlap with each other or with Steps 3/4.
- Designer's spec authoring (Step 1) can begin immediately and in parallel with Coder scaffolding `project-data.json` placeholders, provided the enums are reconciled before Step 3 begins.

## 8. Work that must run sequentially

- Step 1 → Step 3 (Designer spec must exist before HTML implementation).
- Step 3 → Step 4 (CSS targets real markup).
- Steps 2 + 3 + 4 + 5 → Step 6 (integration check requires everything present).
- Any change to the status/priority enum after Step 2 forces re-verification of Steps 3 and 4.

## 9. Edge cases to handle

- **Empty `projects` array** — render a friendly empty state rather than a blank grid.
- **Fetch failure** for `project-data.json` (e.g. opened via `file://`) — show a visible, accessible error message; document that the app must be served, not opened directly.
- **Unknown `status` or `priority` value** — fall back to a neutral badge style, not broken CSS.
- **Long project names / owner strings / activity text** — wrap gracefully; no overflow past card edges.
- **Small viewport (≤360px)** — cards remain readable, badges don't overlap.
- **Color-only encoding** — status and priority must also be conveyed via text/label/icon for accessibility.
- **Reduced motion** — no essential information conveyed through motion; respect `prefers-reduced-motion`.
- **Launch config opens directory listing** — must be prevented; URL must terminate at `index.html`.
- **`.vscode/launch.json` with comments** — invalid; must be strict JSON.
- **Port collision** on the chosen static-serve port — pick a deterministic but uncommon port and document it.

## 10. Validation expectations

- `app/project-data.json` parses as JSON; top-level key `projects` is an array; each entry has all five required fields; `status` and `priority` values are within the agreed enum.
- `app/index.html` uses semantic landmarks, one `<h1>`, logical heading order, and includes both `.dashboard` and `.project-card` hooks.
- `app/styles.css` defines styles for `.dashboard`, `.project-card`, every status modifier, and every priority modifier; passes a quick contrast check on badges (WCAG AA); has at least one responsive breakpoint.
- `.vscode/launch.json` is strict JSON (no comments/trailing commas), contains a configuration named exactly **Run Project Pulse Dashboard**, sets `cwd` to `${workspaceFolder}/app`, and opens `index.html` (URL ends in `/index.html` or equivalent so no directory listing appears).
- End-to-end: launching from VS Code renders a styled dashboard populated from `project-data.json`, with visible status badges and priority treatment, on first paint.

## 11. Open questions

- **Serve mechanism for `launch.json`** — should it use a Node-based static server (`npx serve`), Python's `http.server`, or VS Code's Simple Browser against an existing dev server? Repo currently shows no package manifest, so a zero-install approach (Python `http.server` or a `${command:...}` + Simple Browser combo) may be safest. Orchestrator/learner to confirm.
- **Canonical status enum** — brief lists "status" and "priority or risk level" but does not fix vocabularies. Proposed: `active | at-risk | blocked | paused | shipped` and `high | medium | low`. Needs Designer confirmation before Step 2 finalizes.
- **Contributor-friendly summary** — brief mentions "a short contributor-friendly summary." Is that a per-project field (not in the required schema) or a page-level intro paragraph? Recommend page-level intro to keep the required schema unchanged.
- **`recentActivity` shape** — free-form string, or `{ text, timestamp }`? Recommend a single string for MVP; revisit if Designer needs relative-time formatting.
- **Icon library** — needed for non-color status encoding? Recommend inline SVG or Unicode glyphs to avoid dependencies.
- **Port selection** for the static server — propose `4173` unless the learner prefers otherwise.
