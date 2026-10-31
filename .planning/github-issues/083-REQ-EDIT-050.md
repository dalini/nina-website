## Metadata

- **ID**: REQ-EDIT-050
- **Title**: Accept only images up to 10 MB and 40 megapixels and PDFs up to 20 MB by their actual type, and reject unsanitized SVGs, at upload
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-INPUT-1, SEC-INPUT-2, SEC-INPUT-5, `REF-INPUT`; REQUIREMENTS.md FR-10.2; parent {{ISSUE_URL:REQ-EDIT-000}}

## Requirement

- **Statement**: The Editing Interface MUST accept only image files and PDFs for upload, checking the actual file type and not only the extension; it MUST reject images above 10 MB or above 40 megapixels, PDFs above 20 MB, and SVGs that are not sanitized, telling the editor why.
- **Rationale**: Catches mistakes and malicious files before they reach the Content Store (T-7, T-8); the Site Build rechecks (REQ-IMAGES-040, REQ-IMAGES-050).
- **Assumptions**: None
- **Out of Scope**: The Site Build rechecks (REQ-IMAGES-040, REQ-IMAGES-050).
- **Design Traceability**: N/A
- **Architecture Traceability**: Editing Interface (upload validation); DR-3
- **Security Traceability**: SEC-INPUT-1, SEC-INPUT-2, SEC-INPUT-5 (editor side)

## Scope

- **Applies To**: Web Client
- **Components**: Editing Interface, Content Store
- **Interfaces / Operations**: Image and file upload fields in every collection
- **Actors**: Editors (Nina, Ives)
- **Preconditions**: REQ-EDIT-020, REQ-EDIT-040
- **Data Classification**: Internal
- **Personal or Regulated Data**: Personal Data (editor GitHub identities)
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity, Availability
- **Control Layers**: Input Validation
- **Threat References**: STRIDE Tampering / Denial of Service: SECURITY.md Threat Model v2 T-7, T-8; CWE-434, CWE-409
- **Abuse / Misuse Case**: An executable renamed `.jpg`, an SVG with script or a decompression bomb is uploaded
- **Trust Boundary**: TB-2 (editor browser ↔ Editing Interface), TB-3 (Editing Interface ↔ Content Store)
- **Untrusted Inputs or Assertions**: Everything entered or uploaded in the editor's browser
- **Authoritative Enforcement Point**: Editing Interface (first check); Site Build (authoritative recheck, DR-3)
- **Independent Verification**: The Site Build rechecks the rules (REQ-CONTENT-030)
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: W3C WCAG 2.2, Level AA (DESIGN.md Accessibility target)
- **Mapping Basis**: Other: WCAG 2.2 Level AA as targeted by DESIGN.md.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given a 4 MB JPEG and a 15 MB PDF, when they are uploaded, then both are accepted.
2. **AC-02 — Boundary or failure behavior**: Given an executable renamed `photo.jpg`, a 12 MB image, a 45 MP image of small file size and a 25 MB PDF, when each is uploaded, then each is rejected with a message naming the reason, and nothing is committed.
3. **AC-03 — Boundary or failure behavior**: Given an SVG containing `<script>`, when it is uploaded, then it is rejected.
4. **AC-04 — Prohibited behavior**: Given any rejected upload, when the Content Store is checked, then no commit contains the file.

## Failure Behavior

- **On Invalid Input**: The save is rejected and the form names the missing or invalid field in text (FR-10.6, DESIGN.md Inputs and form feedback); nothing is committed
- **On Authentication Failure**: Editing actions require a signed-in editor (SEC-AUTHN-1); the production sign-in through the OAuth helper is blocked scope (PQ-34) and not part of this issue
- **On Authorization Failure**: A commit by an account that is not on the Content Store allow-list is rejected by GitHub (SEC-AUTHZ-1, REQ-STORE-010); the Editing Interface shows the error and nothing changes
- **On Security-Decision Failure**: N/A: the authoritative checks are the Site Build rechecks
- **On External Dependency Failure**: GitHub API unavailable: the save fails with an error shown to the editor; nothing is half-written; the public site stays online (T-10)
- **On System Error**: A failed save leaves the Content Store unchanged and keeps the editor's input on screen
- **Logging / Audit**: Each save is a commit in the Content Store with the signed-in editor and time (FR-10.7)
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: N/A unless first-party validation code is added; then unit tests for each check
- **Integration Tests**: Scripted uploads against the test instance
- **Security Tests**: Renamed executables, polyglots, SVG with script, decompression bombs
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:EDIT-050/AC-01`; AC-02 → `test:EDIT-050/AC-02`; AC-03 → `test:EDIT-050/AC-03`; AC-04 → `test:EDIT-050/AC-04` (test command TO BE DECIDED by the first coding PR, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test Sveltia CMS instance at the pinned release (REQ-EDIT-010) with the project configuration, working on a test content repository with a test editor account (not `dalini/nina-content`); Chrome, Edge, Firefox and Safari in their last two major versions; keyboard-only check

## Dependencies

- **Upstream Requirements**: REQ-EDIT-020 ({{ISSUE_URL:REQ-EDIT-020}}), REQ-EDIT-040 ({{ISSUE_URL:REQ-EDIT-040}})
- **Downstream Requirements**: None
- **External Dependencies**: GitHub API; Sveltia CMS (pinned release)
- **Dependency Assumptions**: Sveltia CMS commits through the GitHub API as the signed-in editor (ARCHITECTURE.md Editing Interface)
- **Failure Impact**: Editing stops while GitHub is down; the public site stays online (T-10)

## Implementation Notes

- **Constraints**: Sveltia CMS configuration kept in `dalini/nina-site` (ARCHITECTURE.md Editing Interface); content stays portable (DR-4); DEP-1 to DEP-8 apply
- **Prohibited Approaches**: Rules enforced only in the Editing Interface without the Site Build recheck (DR-3); configuration that lets editors write outside content and media (DR-8)
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and REQUIREMENTS.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; NOT VERIFIED, see ISSUE_PLAN.md Planning Rules), because: Security-relevant validation in a third-party UI, where support for content sniffing and pixel checks must be verified.
- **Required Human Review**: Product review by Nina (forms and wording); security review by Ives
- **Open Decisions**: PQ-44 (non-blocking): verify which checks Sveltia CMS can enforce; a check it cannot enforce is raised as an open question, not dropped (the Site Build recheck still applies).
