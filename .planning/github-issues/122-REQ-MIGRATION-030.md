## Metadata

- **ID**: REQ-MIGRATION-030
- **Title**: Give every migrated image interim alt text made of the work title and the view number
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: Medium
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-11.3, FR-3.7, FR-10.6; `.planning/OPEN_QUESTIONS.md` Q-22c; parent https://github.com/dalini/nina-website/issues/17

## Requirement

- **Statement**: The import MUST give every migrated image interim German alt text of the form "<work title>, view <n>", where n is the image's position in the work; this counts as alt text under FR-3.7 and FR-10.6 until Nina improves it.
- **Rationale**: Every image needs alt text (FR-3.7); the legacy alt text is only the title (Q-22c).
- **Assumptions**: None
- **Out of Scope**: Better alt text (Nina, later); view kinds such as "installation view", which the legacy data does not record.
- **Design Traceability**: N/A
- **Architecture Traceability**: Content Store
- **Security Traceability**: N/A

## Scope

- **Applies To**: Background Processing
- **Components**: Content Store
- **Interfaces / Operations**: Alt text of imported images
- **Actors**: Maintainer (Ives), who runs the one-time import
- **Preconditions**: REQ-MIGRATION-010
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

1. **AC-01 — Expected behavior**: Given the legacy work "tiptoe" with two images, when it is imported, then their German alt texts are "tiptoe, view 1" and "tiptoe, view 2".
2. **AC-02 — Boundary or failure behavior**: Given the imported content, when the rule check runs, then no image is reported for missing alt text.
3. **AC-03 — Prohibited behavior**: Given any imported image, when its English alt text is checked, then it is empty (FR-1.7 shows the German one).

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

- **Unit Tests**: Alt text generation per image
- **Integration Tests**: Import plus build; rule check passes
- **Security Tests**: Legacy text containing `<a>`, `<p>` or `<script>` is imported as text and appears encoded in the build output (REQ-OUTPUT-010)
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:MIGRATION-030/AC-01`; AC-02 → `test:MIGRATION-030/AC-02`; AC-03 → `test:MIGRATION-030/AC-03` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A copy of `docs/legacy-content/works/` and the legacy images; a test content repository; the Site Build for the end-to-end check

## Dependencies

- **Upstream Requirements**: REQ-MIGRATION-010 (https://github.com/dalini/nina-website/issues/121)
- **Downstream Requirements**: None
- **External Dependencies**: Git LFS of `dalini/nina-laaf-site` (legacy images)
- **Dependency Assumptions**: The legacy images are still available in that repository's Git LFS (README.md)
- **Failure Impact**: Without the images the import cannot complete

## Implementation Notes

- **Constraints**: One-time import script in `dalini/nina-site` (code repository, CLAUDE.md); output in the documented content model (REQ-CONTENT-010); DEP-1 to DEP-8 apply
- **Prohibited Approaches**: Hand-editing imported files instead of fixing and re-running the import; inventing English values
- **Implementation Guidance**: Q-22c records the format "title, view n"; FR-11.3's example names a view kind, which the legacy files do not carry.
- **AI Development Guidance**: Follow CLAUDE.md and REQUIREMENTS.md FR-11; `docs/legacy-content/` is reference input for the import, not specification; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: A single string rule added to the import.
- **Required Human Review**: Product review by Nina of the imported works
- **Open Decisions**: None
