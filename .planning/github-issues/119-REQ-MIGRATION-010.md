## Metadata

- **ID**: REQ-MIGRATION-010
- **Title**: Import the 53 legacy works with their German title, year, images and image captions into the Content Store
- **Version**: 0.3.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 fourth run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: Medium
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-11.1 (title, year, images, captions), FR-1.7, FR-3.7; ARCHITECTURE.md Content Store (one-time import, DF-3); parent https://github.com/dalini/nina-website/issues/17

## Requirement

- **Statement**: A one-time import MUST create one Work item for each of the 53 legacy work series in `docs/legacy-content/works/`, with its legacy title, its year when present and its images in the legacy order, and MUST keep every non-empty legacy image caption as the caption of its own image; all values go into the German fields and the English fields stay empty.
- **Rationale**: The existing body of work moves to the new site (FR-11.1).
- **Assumptions**: None
- **Out of Scope**: The works' materials and dimensions and the migration report (REQ-MIGRATION-040); publish state and featured work (REQ-MIGRATION-020); alt text (REQ-MIGRATION-030).
- **Design Traceability**: N/A
- **Architecture Traceability**: Content Store (one-time import of the legacy works, DF-3)
- **Security Traceability**: SEC-OUT-1 (legacy text is encoded at build time), SEC-PRIV-4 (legacy EXIF stripped at build time)

## Scope

- **Applies To**: Background Processing
- **Components**: Content Store
- **Interfaces / Operations**: Import script; Work items in the Content Store
- **Actors**: Maintainer (Ives), who runs the one-time import
- **Preconditions**: REQ-CONTENT-010
- **Data Classification**: Public
- **Personal or Regulated Data**: Personal Data (photo credits, if present in legacy captions)
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Input Validation
- **Threat References**: STRIDE Tampering: SECURITY.md Threat Model v3 T-41 (legacy HTML and EXIF)
- **Abuse / Misuse Case**: Legacy text carrying raw HTML is rendered as markup (T-41); it is imported as text and encoded at build time (REQ-OUTPUT-010)
- **Trust Boundary**: DF-3 (legacy import → Content Store)
- **Untrusted Inputs or Assertions**: Legacy front matter and text in `docs/legacy-content/works/` and image files from `dalini/nina-laaf-site`
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

1. **AC-01 — Expected behavior**: Given the 53 legacy files, when the import runs against a test content repository, then 53 Work items exist, each with its legacy title and, for the 41 that have one, its year.
2. **AC-02 — Boundary or failure behavior**: Given a legacy file with images in order 1, 2, 3, when it is imported, then the Work's images keep that order with their image files present.
3. **AC-03 — Boundary or failure behavior**: Given a legacy image file missing from Git LFS, when the import runs, then it stops naming the file and commits nothing.
4. **AC-04 — Prohibited behavior**: Given any imported Work, when its fields are reviewed, then no English value is filled in and no value is invented.
5. **AC-05 — Additional criterion**: Given `faltung` and `aluminiumg-sse`, whose images carry different legacy captions, when they are imported, then each image's German caption is its own legacy caption unchanged, and images without a legacy caption have none.

## Failure Behavior

- **On Invalid Input**: A legacy record that cannot be mapped is reported by file name and field, and nothing is written for it; the import is re-runnable
- **On Authentication Failure**: N/A
- **On Authorization Failure**: N/A: Ives writes to the Content Store with his own account
- **On Security-Decision Failure**: N/A
- **On External Dependency Failure**: Legacy images unavailable from Git LFS: the import stops and reports which files are missing; nothing partial is committed
- **On System Error**: The import writes to a branch or working copy first and commits only a complete result
- **Logging / Audit**: The import prints a summary (works imported, published, unpublished, images) without content values
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: Front-matter mapping per field
- **Integration Tests**: Import into a test repository, then a preview build
- **Security Tests**: Legacy text containing `<a>`, `<p>` or `<script>` is imported as text and appears encoded in the build output (REQ-OUTPUT-010)
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:MIGRATION-010/AC-01`; AC-02 → `test:MIGRATION-010/AC-02`; AC-03 → `test:MIGRATION-010/AC-03`; AC-04 → `test:MIGRATION-010/AC-04`; AC-05 → `test:MIGRATION-010/AC-05` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A copy of `docs/legacy-content/works/` and the legacy images; a test content repository; the Site Build for the end-to-end check

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-010 (https://github.com/dalini/nina-website/issues/18)
- **Downstream Requirements**: REQ-MIGRATION-040 (https://github.com/dalini/nina-website/issues/122), REQ-MIGRATION-020 (https://github.com/dalini/nina-website/issues/123), REQ-MIGRATION-030 (https://github.com/dalini/nina-website/issues/124)
- **External Dependencies**: Git LFS of `dalini/nina-laaf-site` (legacy images)
- **Dependency Assumptions**: The legacy images are still available in that repository's Git LFS (README.md)
- **Failure Impact**: Without the images the import cannot complete

## Implementation Notes

- **Constraints**: One-time import script in `dalini/nina-site` (code repository, CLAUDE.md); output in the documented content model (REQ-CONTENT-010); DEP-1 to DEP-8 apply
- **Prohibited Approaches**: Hand-editing imported files instead of fixing and re-running the import; inventing English values
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and REQUIREMENTS.md FR-11; `docs/legacy-content/` is reference input for the import, not specification; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: A mapping script from a known, uniform source format.
- **Required Human Review**: Product review by Nina of the imported works
- **Open Decisions**: None. Decided in step 10 a third time (`.planning/OPEN_QUESTIONS.md`): PQ-58: materials and dimensions come from the work-level legacy fields, else from a first caption in the form "materials | dimensions | year", else stay empty; a migration report lists what Nina must complete and values that break FR-3.3 (FR-11.1). That part is REQ-MIGRATION-040.
