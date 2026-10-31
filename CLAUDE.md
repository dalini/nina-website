# CLAUDE.md

nina-laaf.de is the public, bilingual (DE/EN) website of artist and sculptor Nina Laaf. The project is specified but not built yet; step 10 resolved the open questions (record: `.planning/OPEN_QUESTIONS.md`). Code lives in `dalini/nina-site` (Hugo templates, config, CI; maintainer only) and content in `dalini/nina-content` (private Content Store); this repository holds the specification.

## Where things are defined

Read only the file the task needs; each owns its domain.

- `REQUIREMENTS.md` — WHAT the system does. Read before changing observable behavior.
- `DESIGN.md` — the design language; `style-guide.html` is its rendered reference. Read before UI work.
- `ARCHITECTURE.md` — components, boundaries, data flow, dependency rules. Read before structural changes.
- `SECURITY.md` — security rules and threat model. Read before touching auth, input handling, data protection, or any trust boundary.
- `REQUIREMENT_TEMPLATE.md` — the required structure for new GitHub issues.
- `docs/` — reference material only (archived project notes, content seed, legacy site content), not spec. Do not treat it as requirements.

## Rules

- Every new GitHub issue MUST follow `REQUIREMENT_TEMPLATE.md`, so each issue is a structured, testable requirement.
- Decisions belong in the spec file that owns the domain, not in `docs/`.

## Commands

In `dalini/nina-site` (Hugo):

- Build: `hugo --minify`
- Run locally: `hugo server`
- Test: TO BE DECIDED (deferred to step 11; the first coding PR sets it).
- Coverage target: 80% line coverage of first-party code, and every acceptance criterion has an automated test.
