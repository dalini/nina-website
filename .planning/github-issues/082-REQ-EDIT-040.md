## Metadata

- **ID**: REQ-EDIT-040
- **Title**: Let editors manage Texts, CV entries, Downloads and Pages with required-field checks and the Texts order
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-10.1, FR-10.2, FR-10.3, FR-10.6, FR-5.1, FR-5.2, FR-5.4, FR-6.1, FR-7.1, FR-7.2, FR-9.1; ARCHITECTURE.md Editing Interface; parent {{ISSUE_URL:REQ-EDIT-000}}

## Requirement

- **Statement**: The Editing Interface MUST let editors create, edit, unpublish and delete Texts (and set their manual order), CV entries (choosing one of the six sections), Downloads (uploading a PDF) and the content of the Pages Home, Contact, Impressum and Datenschutzerklärung, and MUST reject a save of a Text without title or body, a CV entry without section or text or without year outside the short bio, or a Download without title or PDF, naming the missing field.
- **Rationale**: Nina maintains all site text and her CV herself (FR-10.1).
- **Assumptions**: None
- **Out of Scope**: Upload checks (REQ-EDIT-050); rich-text limits (REQ-EDIT-060).
- **Design Traceability**: N/A
- **Architecture Traceability**: Editing Interface; Content Store (Text, CV entry, Download, Page)
- **Security Traceability**: SEC-INPUT-1 (PDF uploads, via REQ-EDIT-050)

## Scope

- **Applies To**: Web Client
- **Components**: Editing Interface, Content Store
- **Interfaces / Operations**: Texts, CV, Downloads and Pages collections in the CMS
- **Actors**: Editors (Nina, Ives)
- **Preconditions**: REQ-EDIT-010, REQ-CONTENT-020
- **Data Classification**: Internal
- **Personal or Regulated Data**: Personal Data (editor GitHub identities)
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Business-Rule Validation
- **Threat References**: STRIDE Tampering: SECURITY.md Threat Model v2 T-12, T-49 (the Site Build rechecks every rule)
- **Abuse / Misuse Case**: An editor account writes content that skips the form rules directly to the Content Store (T-49); the Site Build recheck rejects it
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

1. **AC-01 — Expected behavior**: Given a signed-in test editor, when she adds a CV entry in awards with year and text, a Download with a PDF, and reorders two Texts, then each change is committed in the documented model.
2. **AC-02 — Boundary or failure behavior**: Given a CV entry in education without year, or a Download without a PDF, when she saves, then the save is rejected and the form names the field; a Bio entry without year saves.
3. **AC-03 — Boundary or failure behavior**: Given the Contact page, when she clears the email address and saves, then the next Site Build fails under REQ-CONTENT-030 and the previous site stays online.
4. **AC-04 — Prohibited behavior**: Given the CV section field, when she opens it, then only the six FR-5.2 sections can be chosen.

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

- **Unit Tests**: N/A: configuration
- **Integration Tests**: Scripted browser workflows against the test instance
- **Security Tests**: Content written directly to the test content repository, bypassing the forms, is rejected by the Site Build recheck
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:EDIT-040/AC-01`; AC-02 → `test:EDIT-040/AC-02`; AC-03 → `test:EDIT-040/AC-03`; AC-04 → `test:EDIT-040/AC-04` (test command TO BE DECIDED by the first coding PR, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test Sveltia CMS instance at the pinned release (REQ-EDIT-010) with the project configuration, working on a test content repository with a test editor account (not `dalini/nina-content`); Chrome, Edge, Firefox and Safari in their last two major versions; keyboard-only check

## Dependencies

- **Upstream Requirements**: REQ-EDIT-010 ({{ISSUE_URL:REQ-EDIT-010}}), REQ-CONTENT-020 ({{ISSUE_URL:REQ-CONTENT-020}})
- **Downstream Requirements**: REQ-EDIT-050 ({{ISSUE_URL:REQ-EDIT-050}}), REQ-EDIT-060 ({{ISSUE_URL:REQ-EDIT-060}}), REQ-EDIT-080 ({{ISSUE_URL:REQ-EDIT-080}})
- **External Dependencies**: GitHub API; Sveltia CMS (pinned release)
- **Dependency Assumptions**: Sveltia CMS commits through the GitHub API as the signed-in editor (ARCHITECTURE.md Editing Interface)
- **Failure Impact**: Editing stops while GitHub is down; the public site stays online (T-10)

## Implementation Notes

- **Constraints**: Sveltia CMS configuration kept in `dalini/nina-site` (ARCHITECTURE.md Editing Interface); content stays portable (DR-4); DEP-1 to DEP-8 apply
- **Prohibited Approaches**: Rules enforced only in the Editing Interface without the Site Build recheck (DR-3); configuration that lets editors write outside content and media (DR-8)
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and REQUIREMENTS.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; NOT VERIFIED, see ISSUE_PLAN.md Planning Rules), because: Declarative CMS configuration following the documented model.
- **Required Human Review**: Product review by Nina (forms and wording); security review by Ives
- **Open Decisions**: None
