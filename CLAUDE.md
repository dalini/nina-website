# CLAUDE.md

nina-laaf.de is the public, bilingual (DE/EN) website of artist and sculptor Nina Laaf, presenting her works, exhibitions, texts and CV to curators, galleries, collectors, press and the public. Nina, a non-technical editor, maintains all content through a web-based editing interface. The project is fully specified but not built yet.

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

Build, test and run: TO BE DECIDED.
