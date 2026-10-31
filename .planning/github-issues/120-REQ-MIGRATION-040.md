## Metadata

- **ID**: REQ-MIGRATION-040
- **Title**: Import each legacy work's materials and dimensions from its legacy fields or a clean first caption, and write a migration report for Nina
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 fourth run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: Medium
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-11.1 (materials and dimensions from the legacy `materials` and `dimensions` fields, else from a first caption of the form "materials | dimensions | year", else empty; migration report; step 10 a third time, PQ-58), FR-3.3, FR-1.7; ARCHITECTURE.md Content Store (the import writes a migration report); parent {{ISSUE_URL:REQ-MIGRATION-000}}

## Requirement

- **Statement**: The one-time import MUST give each migrated work its materials and dimensions from its legacy `materials` and `dimensions` fields; a work without them MUST take them from its first image's caption when that caption has the form "materials | dimensions | year" (three parts, the second the dimensions, the third a four-digit year); otherwise they MUST stay empty; values go into the German fields unchanged, and the first caption stays the caption of its own image unchanged in every case; the import MUST write a migration report that lists every work left without materials or without dimensions, and every imported dimensions value that does not meet FR-3.3 (two or three values in centimeters), for Nina to complete in the Editing Interface.
- **Rationale**: 25 of the 53 legacy works hold materials and dimensions only in work-level fields, and many first captions are installation texts or in another form, so a caption-only rule would drop data that exists (PQ-58).
- **Assumptions**: Checked on 2026-10-31 against `docs/legacy-content/works/`: every work-level `dimensions` value and every first caption of the form "materials | dimensions | year" holds two or three values in centimeters, so the imported works pass the dimensions rule of REQ-CONTENT-010 and the report's list of non-conforming values is expected to be empty; the rule still applies to whatever the import finds
- **Out of Scope**: Titles, years, images and captions (REQ-MIGRATION-010); publish state and the featured work (REQ-MIGRATION-020); alt text (REQ-MIGRATION-030); completing the missing values, which Nina does in the Editing Interface (REQ-EDIT-020).
- **Design Traceability**: N/A
- **Architecture Traceability**: Content Store (one-time import of the legacy works, which also writes a migration report, DF-3)
- **Security Traceability**: SEC-OUT-1 (legacy text is encoded at build time)

## Scope

- **Applies To**: Background Processing
- **Components**: Content Store
- **Interfaces / Operations**: Import script (materials and dimensions mapping); the migration report
- **Actors**: Maintainer (Ives), who runs the one-time import and gives the report to Nina
- **Preconditions**: REQ-MIGRATION-010
- **Data Classification**: Public
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Input Validation
- **Threat References**: STRIDE Tampering: SECURITY.md Threat Model v3 T-41 (legacy HTML)
- **Abuse / Misuse Case**: Legacy text carrying raw HTML is rendered as markup (T-41); it is imported as text and encoded at build time (REQ-OUTPUT-010)
- **Trust Boundary**: DF-3 (legacy import → Content Store)
- **Untrusted Inputs or Assertions**: Legacy front matter and captions in `docs/legacy-content/works/`
- **Authoritative Enforcement Point**: The import script, then the Site Build rechecks (REQ-CONTENT-030)
- **Independent Verification**: The imported content passes the same Site Build checks as editor content
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: N/A
- **Mapping Basis**: No external standard applies to the one-time import.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given `heiter-bis-wolkig`, which has the legacy fields "Schwarzstahl, Kupfer, Gips, Lack" and "58 x 75 x 5 cm" and the first caption "Metall, Gips", when it is imported, then its German materials are "Schwarzstahl, Kupfer, Gips, Lack" and its dimensions "58 x 75 x 5 cm", and its first image keeps the caption "Metall, Gips" unchanged.
2. **AC-02 — Expected behavior**: Given `faltung`, which has no legacy materials or dimensions fields and the first caption "Aluminium | 90 x 90 x 8 cm | 2016", when it is imported, then its materials are "Aluminium" and its dimensions "90 x 90 x 8 cm", and the caption stays on its image unchanged.
3. **AC-03 — Boundary or failure behavior**: Given `coc-n` ("Cocón | Filzobjekt | 4x70x170 | 2012", four parts), `raumforderung` (five parts) and `eat-sleep-work-repeat` ("Lack, Aluminium getrieben | 2018 | 90 x 120 x5 cm", the year in second place), and a work whose first caption is an installation text, when they are imported, then their materials and dimensions stay empty, their captions stay unchanged, and each is listed in the migration report as a work without materials and dimensions.
4. **AC-04 — Boundary or failure behavior**: Given a test legacy file whose `dimensions` field is "Ø 30 cm" or holds four values, when it is imported, then the value is imported unchanged and listed in the migration report as not meeting FR-3.3, naming the work.
5. **AC-05 — Prohibited behavior**: Given any imported work, when its fields are reviewed, then no materials or dimensions value is invented, converted, reordered or translated, no English value is filled in (FR-1.7 applies), and writing the report adds no content item to the Content Store and does not make the next publish fail.
6. **AC-06 — Additional criterion**: Given the full legacy import, when it finishes, then the migration report lists, for every work without materials or without dimensions, its title and which of the two is missing, plus every non-conforming dimensions value, in a form Ives can hand to Nina.

## Failure Behavior

- **On Invalid Input**: A caption that does not have the form leaves materials and dimensions empty and lists the work in the report; nothing is guessed
- **On Authentication Failure**: N/A
- **On Authorization Failure**: N/A: Ives writes to the Content Store with his own account
- **On Security-Decision Failure**: N/A
- **On External Dependency Failure**: N/A: reads only the files in `docs/legacy-content/works/`
- **On System Error**: The import writes to a branch or working copy first and commits only a complete result together with a complete report
- **Logging / Audit**: The import prints a summary (works with materials and dimensions from fields, from captions, left empty) without content values
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: Caption parsing (three parts in order, four or five parts, year in another position, empty, installation text); field precedence over the caption; FR-3.3 check of dimensions values
- **Integration Tests**: Import of the full legacy set into a test repository; report compared with the legacy data; preview build of the result
- **Security Tests**: Legacy text containing `<a>` or `<script>` in materials is imported as text and appears encoded in the build output (REQ-OUTPUT-010)
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:MIGRATION-040/AC-01`; AC-02 → `test:MIGRATION-040/AC-02`; AC-03 → `test:MIGRATION-040/AC-03`; AC-04 → `test:MIGRATION-040/AC-04`; AC-05 → `test:MIGRATION-040/AC-05`; AC-06 → `test:MIGRATION-040/AC-06` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A copy of `docs/legacy-content/works/`; a test content repository; the Site Build for the end-to-end check

## Dependencies

- **Upstream Requirements**: REQ-MIGRATION-010 ({{ISSUE_URL:REQ-MIGRATION-010}})
- **Downstream Requirements**: None
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: Without it, migrated works show no materials or dimensions until Nina enters them by hand without a list of what is missing

## Implementation Notes

- **Constraints**: Part of the one-time import script in `dalini/nina-site`; values are German, English fields stay empty (FR-11.1); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Taking materials or dimensions from a caption when the work has legacy fields; parsing captions in another form by guessing the order; normalizing dimensions values (they are shown as entered, FR-3.3); writing the report as a content item
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and REQUIREMENTS.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: A one-time mapping with a precise precedence rule and known fixture data.
- **Required Human Review**: Product review by Nina (the migration report and the imported values)
- **Open Decisions**: PQ-68 (non-blocking, TO BE DECIDED): whether a first caption with stray characters around its parts counts as the form "materials | dimensions | year" (e.g. `aluminiumg-sse`: "Aluminiumguss | 68 x 14 x 4 cm | 2018" followed by a literal `\n`). PQ-69 (non-blocking, TO BE DECIDED): where the migration report is written and in which format Ives hands it to Nina (FR-11.1 names neither). PQ-58 (decided in step 10 a third time): see Source.
