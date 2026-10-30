Decompose the repository specification into one GitHub parent epic and implementation-ready sub-issues.

## Run Inputs

- `Scope: ALL` — or a comma-separated list of requirement IDs from `REQUIREMENTS.md`
- `Execution mode: DRAFT_ONLY` — `DRAFT_ONLY` never touches GitHub; `CREATE` authorizes issue creation via the GitHub CLI

## Sources

Read all five: `REQUIREMENTS.md`, `ARCHITECTURE.md`, `SECURITY.md`, `DESIGN.md`, `REQUIREMENT_TEMPLATE.md`. Follow `CLAUDE.md` if present, but it is not product specification. UI-facing issues should also cite `style-guide.html` as the rendered design reference.

If any required file is missing, create nothing and output exactly:

`ERROR: Missing or unreadable required repository file(s): <file names>`

Each document is authoritative for its own domain. Reading order never resolves a conflict.

When documents conflict or a required decision is missing:
- Record it under `## Open Questions` (`PQ-1`, `PQ-2`, ...) with the exact IDs and sections affected.
- Never choose an interpretation. Never fabricate scope.

A question is BLOCKING when it affects observable behavior, roles or permissions, auth, API contracts, data semantics, trust boundaries, security controls, failure behavior, or acceptance testing.

If blocking questions remain:
- `DRAFT_ONLY`: mark the blocked scope, decompose the rest.
- `CREATE`: stop before any GitHub change.

## Decomposition

Before drafting: map every in-scope requirement to its architecture components, security rules, and design constraints. Build a coverage matrix and an acyclic dependency graph.

Structure: one root epic, optional workstream issues, and leaf issues as the implementation units.

Every leaf issue MUST be:
- **Single-responsibility** — one coherent behavior, endpoint, control, workflow, or data flow
- **Deterministic** — no undocumented product or architecture decisions needed
- **Bounded** — half a day to two engineer-days, and at most 1,500 changed human-authored lines (estimate both as ranges; exclude generated files and lockfiles)
- **Independently testable** — Given/When/Then criteria covering expected, invalid, unauthorized, and failure behavior
- **Traceable** — exact requirement, architecture, security, and design references; no invented IDs, schemas, or technology choices
- **Complete** — implementation, tests, error handling, security, and accessibility in the same issue Never create "Build the backend," "Add security," or "Write tests" issues, and never defer security or tests into cleanup issues.

Split at behavioral boundaries when an issue exceeds either size limit, crosses unrelated boundaries, or bundles independently releasable behaviors. When in doubt, split.

## IDs, Titles, Bodies

- Root epic: `REQ-EPIC-001`
- Workstreams: `REQ-<CAPABILITY>-000`
- Leaves: `REQ-<CAPABILITY>-010` onward
- Titles: `[REQ-AUTH-010] Enforce authorization for account retrieval`
- Capability names come from the source documents Every issue body follows `REQUIREMENT_TEMPLATE.md`:
- All headings in order, every applicable field populated
- No bracketed placeholders
- `N/A` only when a field truly does not apply
- `TO BE DECIDED` only for non-blocking decisions
- Acceptance criteria never add behavior beyond the sources

## Coverage and Model Assignment

Track every requirement in a coverage table: Requirement ID | Issue IDs | Boundary | Security rules | Design sections | Status (`COVERED`, `PARTIALLY COVERED`, `BLOCKED`, `OUT OF SCOPE`).

Every issue traces to a requirement. Every requirement traces to an issue or a blocking question.

Recommend one model per implementation issue, with a rationale specific to that issue:
- **Claude Fable** — long-horizon, cross-boundary, highly autonomous work
- **Claude Opus** — complex bounded implementation, security-sensitive code
- **Claude Sonnet** — routine, well-specified, single-responsibility leaf issues; the sensible default for most of them
- **OpenAI GPT** — execution through OpenAI tooling; record the exact model, never "ChatGPT"

Verify exact model names against official docs at execution time, or mark `NOT VERIFIED`.

## Artifacts

Write all drafts to `.planning/github-issues/`:
- `ISSUE_PLAN.md` — open questions, coverage, hierarchy, manifest with effort and LOC estimates, topological creation order
- `000-REQ-EPIC-001.md` plus one body file per issue Never modify the specification files.

## DRAFT_ONLY Mode

- Local artifacts only; no mutating `gh` commands
- Use `{{ISSUE_URL:<ID>}}` placeholders in task lists
- Include each proposed command in `ISSUE_PLAN.md`:

`gh issue create --title "[REQ-AUTH-010] ..." --body-file ".planning/github-issues/010-REQ-AUTH-010.md"`

## CREATE Mode

Pre-flight checks first:
1. `gh auth status`
2. Confirm the target repository is the intended one
3. Confirm CLI support for `--body-file` and `--parent`
4. Confirm no existing issue reuses a planning ID for different scope Then: create the epic, workstreams, and leaves in topological order with `--parent`; capture every URL; replace all placeholders; link dependencies. When drafts already exist under `.planning/github-issues/` from an earlier run, create from those bodies as-is — re-decompose only if the specs changed after they were drafted.

On any failure: stop, never auto-delete created issues, record the partial state. Never claim a GitHub action succeeded without a verified command result.

## Final Response

- `DRAFT_ONLY`: report the plan path and draft issue count, and surface any blocking `PQ-*` questions that must be answered before a CREATE run
- `CREATE`: output the epic URL, counts of created/reused/failed issues, and the `ISSUE_PLAN.md` path
