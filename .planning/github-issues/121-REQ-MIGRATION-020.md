## Metadata

- **ID**: REQ-MIGRATION-020
- **Title**: Import legacy works without a year as unpublished, publish the rest, and feature the newest work
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: Medium
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-11.2, FR-11.4, FR-10.3, FR-10.6; parent https://github.com/dalini/nina-website/issues/17

## Requirement

- **Statement**: The import MUST mark the 12 legacy works without a year as unpublished and the other 41 as published, MUST ignore the legacy featured flag, and MUST make "vom Flüstern und Formen" (2023), the newest migrated work, the featured work.
- **Rationale**: Works without a year cannot be published (FR-10.6); one work is featured at launch (FR-11.4).
- **Assumptions**: None
- **Out of Scope**: Adding years later (Nina, in the Editing Interface).
- **Design Traceability**: N/A
- **Architecture Traceability**: Content Store
- **Security Traceability**: SEC-INPUT-4 (unpublished works never in production, via REQ-CONTENT-040)

## Scope

- **Applies To**: Background Processing
- **Components**: Content Store
- **Interfaces / Operations**: Import script settings for publish state and featured work
- **Actors**: Maintainer (Ives), who runs the one-time import
- **Preconditions**: REQ-MIGRATION-010, REQ-CONTENT-040
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

1. **AC-01 — Expected behavior**: Given the import ran, when the Content Store is checked, then 41 works are published, 12 are unpublished, and only "vom Flüstern und Formen" is featured.
2. **AC-02 — Boundary or failure behavior**: Given the production build of the imported content, when it runs, then it succeeds and none of the 12 unpublished works appears on the public site.
3. **AC-03 — Prohibited behavior**: Given legacy works with `featured: true`, when they are imported, then that flag has no effect.

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

- **Unit Tests**: Publish-state and featured rules
- **Integration Tests**: Import plus production and preview builds
- **Security Tests**: Legacy text containing `<a>`, `<p>` or `<script>` is imported as text and appears encoded in the build output (REQ-OUTPUT-010)
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:MIGRATION-020/AC-01`; AC-02 → `test:MIGRATION-020/AC-02`; AC-03 → `test:MIGRATION-020/AC-03` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A copy of `docs/legacy-content/works/` and the legacy images; a test content repository; the Site Build for the end-to-end check

## Dependencies

- **Upstream Requirements**: REQ-MIGRATION-010 (https://github.com/dalini/nina-website/issues/121), REQ-CONTENT-040 (https://github.com/dalini/nina-website/issues/23)
- **Downstream Requirements**: None
- **External Dependencies**: Git LFS of `dalini/nina-laaf-site` (legacy images)
- **Dependency Assumptions**: The legacy images are still available in that repository's Git LFS (README.md)
- **Failure Impact**: Without the images the import cannot complete

## Implementation Notes

- **Constraints**: One-time import script in `dalini/nina-site` (code repository, CLAUDE.md); output in the documented content model (REQ-CONTENT-010); DEP-1 to DEP-8 apply
- **Prohibited Approaches**: Hand-editing imported files instead of fixing and re-running the import; inventing English values
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and REQUIREMENTS.md FR-11; `docs/legacy-content/` is reference input for the import, not specification; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Two fixed rules added to the import.
- **Required Human Review**: Product review by Nina of the imported works
- **Open Decisions**: None
