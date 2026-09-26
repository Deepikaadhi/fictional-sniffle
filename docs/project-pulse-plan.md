# Project Pulse dashboard implementation plan

## Summary

Mona's team needs a small static Project Pulse dashboard that a contributor can launch from VS Code and immediately see which projects are active, who owns them, their status, recent activity, and priority. The deliverable is four files: `app/index.html`, `app/styles.css`, `app/project-data.json`, and `.vscode/launch.json`. Work is split between the Designer (visual layout, cards, badges, hierarchy, accessibility, responsive behavior) and the Coder (data file, static markup wiring, launch configuration, validation). The Orchestrator sequences the phases so the data contract exists before UI work, HTML structure exists before CSS polish, and the launch configuration is written after the entry page (`app/index.html`) is confirmed as the target.

## File assignments

Each file has one primary owner to keep file scopes non-overlapping.

- **`app/project-data.json`** — Primary owner: **Coder**. Defines the data contract that the dashboard consumes. Top-level `projects` array; each entry has `name`, `owner`, `status`, `recentActivity`, `priority`.
- **`app/index.html`** — Primary owner: **Designer**. Owns semantic structure, information hierarchy, project card markup, status badges, priority treatment, and accessibility landmarks. Coder may make a small scripted follow-up only if data wiring requires it, but structure and hooks are Designer-owned.
- **`app/styles.css`** — Primary owner: **Designer**. Owns visual polish: `.dashboard` and `.project-card` selectors, spacing, typography, color, `border-radius`, `box-shadow`, status/priority badge styling, and responsive layout.
- **`.vscode/launch.json`** — Primary owner: **Coder**. Strict JSON, no comments. Configuration name **`Run Project Pulse Dashboard`**, `cwd` set to `${workspaceFolder}/app`, and opens `index.html` (not a directory listing) so the learner can launch the dashboard from VS Code without manual file browsing.

## Designer responsibilities

The Designer produces the Project Pulse frontend so the first view clearly reads as a dashboard, not a bare HTML page.

- **Layout**: A `.dashboard` container with a header (product name, short contributor-friendly summary) and a responsive grid of project cards below it. Comfortable, readable spacing between cards.
- **Project cards**: One `.project-card` per project. Each card visibly shows project `name`, `owner`, `status`, `recentActivity`, and `priority`. Card uses `border-radius` and `box-shadow` for a polished, elevated look.
- **Status badges**: Distinct, high-contrast badge treatments per status value (for example: active, at risk, blocked, shipped). Uses color plus a text label so meaning is not conveyed by color alone.
- **Priority treatment**: Priority is visually distinct from status (for example: pill, tag, or accent stripe) so a scanner can pick out high-priority projects quickly.
- **Information hierarchy**: Project name is the strongest visual element on a card; owner and status sit close to it; recent activity reads as secondary metadata.
- **Accessibility**: Semantic landmarks (`<header>`, `<main>`), heading order, `aria-label` on the dashboard region, sufficient color contrast, focusable/visible focus styles, and non-color status cues.
- **Responsive behavior**: Cards reflow on narrow viewports (single column on mobile, multi-column on wider screens) without horizontal scroll.
- **Visual polish**: Consistent typography scale, restrained color palette, hover/focus affordances, and CSS hooks (`.dashboard`, `.project-card`) that are deterministic for downstream validation.

## Coder responsibilities

The Coder implements the static, runnable pieces so the dashboard is launchable and its data contract is stable.

- **Static data file (`app/project-data.json`)**: Valid, strict JSON with a top-level `projects` array. Every project object contains `name`, `owner`, `status`, `recentActivity`, and `priority`. Provides a small, contributor-friendly set of realistic sample projects so the Designer's UI has meaningful content to render.
- **Data contract stewardship**: Locks the field names and shapes so the Designer can rely on them for markup and CSS hooks. Any schema change is coordinated before UI work continues.
- **Launch configuration (`.vscode/launch.json`)**: Strict JSON with no comments. Includes a configuration named `Run Project Pulse Dashboard` that serves the `app/` directory (`cwd`: `${workspaceFolder}/app`) and opens `index.html`, not a directory listing. Deterministic port and URL so the learner sees the dashboard immediately after pressing Run.
- **Runnable app validation**: Confirms `python3 -m json.tool` parses both JSON files, confirms `index.html` opens through the launch configuration and renders the dashboard (not a file index), and confirms all five required fields appear on the rendered cards.
- **Scope discipline**: Does not modify Designer-owned CSS aesthetics or restructure the semantic HTML beyond what is required to wire in data or launch behavior.

## Dependencies

Ordering that the Orchestrator must respect:

1. **Data model before UI.** `app/project-data.json` (and the agreed field list) exists before the Designer commits to card markup or CSS hooks. The Designer needs to know that `name`, `owner`, `status`, `recentActivity`, and `priority` are the visible fields.
2. **HTML before CSS.** `app/index.html` structure and class hooks (`.dashboard`, `.project-card`, badge/priority classes) are defined before `app/styles.css` styles them. CSS depends on selector names that HTML establishes.
3. **Entry page before launch config.** `.vscode/launch.json` is authored after the app's entry file is confirmed to be `app/index.html` and after the serving directory is confirmed to be `app/`. Otherwise the launch configuration risks pointing at the wrong file or a directory listing.
4. **Validation after integration.** End-to-end validation (JSON parse, launch config parse, dashboard render check) runs after all four files exist together.

## Parallel work decisions

File scopes are chosen so parallel work never touches the same file.

- **Sequential (must run first)**: Coder produces `app/project-data.json` and the Orchestrator confirms the field list. This unblocks both the Designer and the launch configuration decision.
- **Parallel (safe after data contract is set)**:
  - Designer works on `app/index.html` structure and `app/styles.css` visual layer. These are Designer-owned and do not conflict with Coder-owned files.
  - Coder, in parallel, prepares `.vscode/launch.json` skeleton (name, `cwd`, serve/open behavior) using the already-known entry point `app/index.html`.
- **Sequential (must run last)**: Integration check — Coder validates the JSON files, confirms the launch configuration opens `index.html` (not a directory listing), and confirms the rendered UI shows all five fields per card. If a mismatch surfaces (for example, a Designer selector name changes), the Orchestrator sequences a short follow-up phase rather than parallel edits.

## Validation expectations

Concrete checks that must pass before the plan is considered delivered.

- **JSON contract**:
  - `app/project-data.json` parses under `python3 -m json.tool`.
  - Top-level key is a `projects` array.
  - Every element contains `name`, `owner`, `status`, `recentActivity`, and `priority` (non-empty).
- **Rendered dashboard**:
  - `app/index.html` renders one project card per entry in `projects`.
  - Each card visibly displays `name`, `owner`, `status`, `recentActivity`, and `priority`.
  - Status appears as a badge; priority is visually distinguishable from status.
  - Markup includes deterministic CSS hooks `.dashboard` and `.project-card`.
  - `app/styles.css` applies `border-radius` and `box-shadow` for the polished card treatment and provides a responsive grid.
- **Launch configuration**:
  - `.vscode/launch.json` parses under `python3 -m json.tool`.
  - Contains a configuration named exactly `Run Project Pulse Dashboard`.
  - `cwd` is `${workspaceFolder}/app`.
  - Opens `index.html` in the browser (not a directory listing).
- **Learner experience**:
  - The dashboard is launchable from VS Code's Run menu without manual file browsing.
  - First view clearly reads as a Project Pulse dashboard (header, cards, badges), not a plain HTML page or a file index.

## Open questions

- Are the exact allowed values of `status` and `priority` fixed (for example, `active`/`at risk`/`blocked`/`shipped` and `high`/`medium`/`low`), or is the Designer free to choose the vocabulary as long as badges are legible?
- Should `recentActivity` be a short free-text string, or a structured object with a timestamp? A short human-readable string is assumed here; confirm before locking the schema.
- Preferred local server for the launch configuration (for example, `python3 -m http.server` versus VS Code's built-in preview) — either satisfies the `cwd` + `index.html` requirement, but the Coder should pick one deterministically.