## Metadata

- **ID**: REQ-IMAGES-050
- **Title**: Recheck the type and size of every file the Site Build publishes and reject SVGs with active content
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-INPUT-1, SEC-INPUT-2, `REF-INPUT`, `REF-XSS`; ARCHITECTURE.md DR-3; parent https://github.com/dalini/nina-website/issues/7

## Requirement

- **Statement**: The Site Build MUST check every file from the Content Store that it would publish: it MUST be an image or a PDF by its actual content (not only its extension), images at most 10 MB and PDFs at most 20 MB; an SVG MUST be rejected unless it is sanitized (no script, event handlers or external references); a failed check fails the publish (SEC-INPUT-3).
- **Rationale**: The upload checks run in the editor's browser and a direct write bypasses them (T-7, T-49).
- **Assumptions**: None
- **Out of Scope**: The site's own `logo.svg` (SEC-INPUT-2); editor-side checks (REQ-EDIT-050).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build (rechecks upload rules for every published file, DR-3)
- **Security Traceability**: SEC-INPUT-1, SEC-INPUT-2

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build
- **Interfaces / Operations**: File check stage before publishing
- **Actors**: Editors (authors of untrusted content); build identity (SEC-CICD-1)
- **Preconditions**: REQ-CONTENT-030
- **Data Classification**: Multiple
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Input Validation
- **Threat References**: STRIDE Tampering: SECURITY.md Threat Model v3 T-7, T-49; CWE-434
- **Abuse / Misuse Case**: An executable renamed `cv.pdf` or an SVG with `<script>` is committed directly to the Content Store
- **Trust Boundary**: TB-4 (Content Store → Site Build)
- **Untrusted Inputs or Assertions**: Every file and field in the Content Store checkout, including direct writes that bypassed the Editing Interface (T-49)
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

1. **AC-01 — Expected behavior**: Given a 3 MB JPEG and a 5 MB PDF, when the build runs, then both pass.
2. **AC-02 — Boundary or failure behavior**: Given an executable renamed `photo.jpg`, a 12 MB image and a 25 MB PDF committed directly, when the build runs, then it fails naming each file and the rule.
3. **AC-03 — Boundary or failure behavior**: Given an SVG containing `<script>` or an `onload` attribute or an external `href`, when the build runs, then it fails; every other SVG fails too: the Site Build has no SVG sanitizer, so no SVG is accepted (SEC-INPUT-2 allows rejection; changed in implementation, dalini/nina-site#25).
4. **AC-04 — Prohibited behavior**: Given any file type other than images and PDFs among the files to publish, when the build runs, then it is not published and the build fails naming it.

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

- **Unit Tests**: Content sniffing by magic bytes; size limits; SVG active-content detection
- **Integration Tests**: Build with renamed, oversized and malicious fixtures written directly to the test content repository
- **Security Tests**: Polyglot files (valid image header with appended payload) and SVG obfuscation variants
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:IMAGES-050/AC-01`; AC-02 → `test:IMAGES-050/AC-02`; AC-03 → `test:IMAGES-050/AC-03`; AC-04 → `test:IMAGES-050/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model, including hostile fixtures

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-030 (https://github.com/dalini/nina-website/issues/22)
- **Downstream Requirements**: REQ-IMAGES-030 (https://github.com/dalini/nina-website/issues/45), REQ-DOWNLOADS-010 (https://github.com/dalini/nina-website/issues/56)
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: A failure of the check blocks publishing until the content is fixed; the previous site stays online

## Implementation Notes

- **Constraints**: Hugo, pinned at a version without known symlink-confinement advisories (`REF-HUGO-ADV`, REQ-PIPELINE-030); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Checks that run only in the editor's browser (DR-3); logging content values or secrets
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Security recheck with polyglot and SVG edge cases; must not trust extensions.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
