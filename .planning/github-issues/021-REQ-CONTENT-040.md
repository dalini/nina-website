## Metadata

- **ID**: REQ-CONTENT-040
- **Title**: Exclude unpublished content from the production output
- **Version**: 0.3.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-INPUT-4; REQUIREMENTS.md FR-10.1, FR-11.2; ARCHITECTURE.md Site Build (Production and preview); parent {{ISSUE_URL:REQ-CONTENT-000}}

## Requirement

- **Statement**: The production build MUST NOT include any unpublished item in its output: no page, no listing or group entry, no image or PDF file, no CV line, no link from another item, and no entry in generated lists such as a sitemap or feed.
- **Rationale**: Unpublished items include the 12 legacy works without a year (FR-11.2) and drafts; only editors may see them, on the preview site (SEC-CICD-3).
- **Assumptions**: None
- **Out of Scope**: The preview build that includes them (REQ-PIPELINE-060).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build (production build of `main` publishes only published content)
- **Security Traceability**: SEC-INPUT-4

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build
- **Interfaces / Operations**: Production Site Build output
- **Actors**: Editors (authors of untrusted content); build identity (SEC-CICD-1)
- **Preconditions**: REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050
- **Data Classification**: Multiple
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Confidentiality
- **Control Layers**: Data Protection
- **Threat References**: STRIDE Information Disclosure: SECURITY.md Threat Model v3 T-21, T-25, T-29; CWE-200
- **Abuse / Misuse Case**: A visitor guesses the URL or image path of an unpublished work
- **Trust Boundary**: TB-4 (Content Store → Site Build)
- **Untrusted Inputs or Assertions**: Publish state of every content item
- **Authoritative Enforcement Point**: Site Build
- **Independent Verification**: Rechecks in the Site Build, independent of the checks in the editor's browser (DR-3)
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP Input Validation Cheat Sheet (`REF-INPUT`)
- **Mapping Basis**: Other: `REF-INPUT` as cited in SECURITY.md for this rule.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given one unpublished work with two images, when the production build runs, then the output has no page for it, no Works or public-space entry, and neither image file.
2. **AC-02 — Boundary or failure behavior**: Given an unpublished work linked from a published Exhibition entry and marked in public space, when the production build runs, then the entry shows no link to it and the CV has no line for it.
3. **AC-03 — Boundary or failure behavior**: Given an unpublished Text, Download and News item, when the production build runs, then none of their pages, files or listing entries exist, and no sitemap, feed or index lists them.
4. **AC-04 — Prohibited behavior**: Given the production output, when every file is searched for the unpublished items' identifiers, titles and file names, then nothing is found.

## Failure Behavior

- **On Invalid Input**: The Site Build fails, names the content item and field, and publishes nothing; the previous site stays online (SEC-INPUT-3)
- **On Authentication Failure**: N/A: the Site Build has no sign-in; it runs as the build identity
- **On Authorization Failure**: N/A: no authorization decision is made in this step
- **On Security-Decision Failure**: Deny by default: if the check cannot complete, the build fails and publishes nothing
- **On External Dependency Failure**: N/A
- **On System Error**: The Site Build fails and publishes nothing; the previous site stays online (SEC-INPUT-3)
- **Logging / Audit**: The build log names the content item identifier and field; it contains no content values beyond identifiers and no secrets (SEC-SECRET-1, SEC-CICD-9)
- **Alerting**: A failed build emails Ives (REQ-PIPELINE-110); Nina sees it in the Editing Interface (REQ-EDIT-090)

## Test Strategy

- **Unit Tests**: Publish-state filter applied to every collection and every cross-reference
- **Integration Tests**: Production build of fixture content with unpublished items of every type; full-text search of the output
- **Security Tests**: Search of the output for unpublished identifiers and media file names; HTTP requests for guessed paths return 404 on the test host
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:CONTENT-040/AC-01`; AC-02 → `test:CONTENT-040/AC-02`; AC-03 → `test:CONTENT-040/AC-03`; AC-04 → `test:CONTENT-040/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model, including hostile fixtures

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-010 ({{ISSUE_URL:REQ-CONTENT-010}}), REQ-CONTENT-020 ({{ISSUE_URL:REQ-CONTENT-020}}), REQ-CONTENT-050 ({{ISSUE_URL:REQ-CONTENT-050}})
- **Downstream Requirements**: REQ-WORKS-010 ({{ISSUE_URL:REQ-WORKS-010}}), REQ-EXHIBITIONS-020 ({{ISSUE_URL:REQ-EXHIBITIONS-020}}), REQ-CV-020 ({{ISSUE_URL:REQ-CV-020}}), REQ-TEXTS-010 ({{ISSUE_URL:REQ-TEXTS-010}}), REQ-DOWNLOADS-010 ({{ISSUE_URL:REQ-DOWNLOADS-010}}), REQ-PIPELINE-060 ({{ISSUE_URL:REQ-PIPELINE-060}}), REQ-MIGRATION-020 ({{ISSUE_URL:REQ-MIGRATION-020}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: A failure of the check blocks publishing until the content is fixed; the previous site stays online

## Implementation Notes

- **Constraints**: Hugo, pinned at a version without known symlink-confinement advisories (`REF-HUGO-ADV`, REQ-PIPELINE-030); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Checks that run only in the editor's browser (DR-3); logging content values or secrets
- **Implementation Guidance**: Hugo copies page resources and builds sitemaps and RSS on its own; check each output kind, not only the templates.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: A confidentiality control with subtle failure modes: generators copy resources and build sitemaps and feeds on their own (T-25, T-29).
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
