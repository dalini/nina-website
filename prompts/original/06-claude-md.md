Create `CLAUDE.md` and `AGENTS.md` in the repository root, optimized for Claude Code.

The repository is a web and API application that has been specified but not built yet. The specification files are `REQUIREMENTS.md`, `DESIGN.md`, `ARCHITECTURE.md`, `SECURITY.md`, and `REQUIREMENT_TEMPLATE.md`, plus `style-guide.html`, the working rendering of the design tokens. Read all of them before writing anything. If any is missing, stop and report the error instead of generating the files. Do not invent workflow, tooling, or process details those documents don't define; mark undecided workflow items `TO BE DECIDED`.

## CLAUDE.md

`CLAUDE.md` loads into every session, so every line must earn its place. Keep it lightweight and specific:
- Two or three sentences on what the project is, distilled from `REQUIREMENTS.md`.
- An ownership map instead of imports. Do not `@`-import the spec files — they are long, and most sessions need only one of them. List each file with its domain and when to read it:
  - `REQUIREMENTS.md` — WHAT the system does; read before changing observable behavior.
  - `DESIGN.md` — the design language (`style-guide.html` is its rendered reference); read before UI work.
  - `ARCHITECTURE.md` — components, boundaries, data flow, dependency rules; read before structural changes.
  - `SECURITY.md` — security rules and threat model; read before touching auth, input handling, data protection, or any trust boundary.
  - `REQUIREMENT_TEMPLATE.md` — the required structure for new GitHub issues.
- This rule: every new GitHub issue MUST follow `REQUIREMENT_TEMPLATE.md` so each issue is a structured, testable requirement.
- Only rules the spec files don't already cover. Never restate their content — each rule lives in exactly one place.

No filler, no restated-obvious guidance, no historical or meta commentary. Build, test, and run commands get added here when they exist; don't invent them now.

## AGENTS.md

Minimal: point to `CLAUDE.md` as the canonical agent instructions. No duplicated content.
