# CLAUDE.md

nina-laaf.de is the public, bilingual (DE/EN) website of artist and sculptor Nina Laaf. The project is specified and being built (step 11): one pull request per issue in `dalini/nina-site`, closing the issue here; step 10, step 10 again and step 10 a third time resolved the open questions (record: `.planning/OPEN_QUESTIONS.md`). Code lives in `dalini/nina-site` (Hugo templates, config, CI, the Deno OAuth helper; maintainer only) and content in `dalini/nina-content` (private Content Store); this repository holds the specification.

## Where things are defined

Read only the file the task needs; each owns its domain.

- `REQUIREMENTS.md` — WHAT the system does. Read before changing observable behavior.
- `DESIGN.md` — the design language; `style-guide.html` is its rendered reference. Read before UI work.
- `ARCHITECTURE.md` — components, boundaries, data flow, dependency rules. Read before structural changes.
- `SECURITY.md` — security rules and threat model. Read before touching auth, input handling, data protection, or any trust boundary.
- `REQUIREMENT_TEMPLATE.md` — the required structure for new GitHub issues.
- `docs/` — reference material only (archived project notes, content seed, legacy site content), not spec. Do not treat it as requirements. Exception: `docs/ops/` holds operational runbooks and review records (no secrets or personal data).

## Rules

- Every new GitHub issue MUST follow `REQUIREMENT_TEMPLATE.md`, so each issue is a structured, testable requirement.
- Decisions belong in the spec file that owns the domain, not in `docs/`.

## Commands

In `dalini/nina-site` (Hugo):

Hugo never reads the Content Store directly: the Site Build checks the content, stages it into a temporary Hugo project and renders that, so plain `hugo` commands do not apply. `dalini/nina-site/CLAUDE.md` has the full list.

- Install the pinned, checksum-verified Hugo: `tools/install-hugo.sh`.
- Build: `deno task build <path-to-nina-content> <output-dir> [production|preview]` (on any content violation it lists all of them, exits 1 and writes nothing).
- Check content only: `deno task check-content <path-to-nina-content>`.
- Test: `deno task test` (Deno's test runner with coverage; fails below 80% line coverage of first-party code). Every acceptance criterion has an automated test named after its issue and criterion (`REQ-CONTENT-010 AC-02: …`).
- Temporary files: tests and builds write them to `.tmp/` in `dalini/nina-site` (`TMPDIR`), never to `/tmp`, which in the development session is a 2 GB RAM disk; `.tmp/` is emptied before each test run and deleted after a passing one. Keep it so for any new test or script.
- Format and lint: `deno fmt --check`, `deno lint` (Hugo templates in `site/layouts/` are excluded from `deno fmt`, which corrupts template syntax).
