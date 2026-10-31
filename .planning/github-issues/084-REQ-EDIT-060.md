## Metadata

- **ID**: REQ-EDIT-060
- **Title**: Offer only the restricted rich-text format in the editor and display content in every Editing Interface view without running markup
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-OUT-1, SEC-OUT-4, `REF-XSS`; parent {{ISSUE_URL:REQ-EDIT-000}}

## Requirement

- **Statement**: The Editing Interface MUST offer rich text only in Text bodies, Page content, News text, a work's short text and the CV bio, with only the SEC-OUT-1 elements (images only in Text bodies, from the Content Store), and MUST display content in lists, forms and previews with the same encoding and restricted rules as SEC-OUT-1, so that markup in content never runs.
- **Rationale**: Script stored in content must not run when another editor opens it (T-5); in this origin it could steal the token (T-45).
- **Assumptions**: None
- **Out of Scope**: Displaying the publish status file as text (blocked scope, PQ-32).
- **Design Traceability**: N/A
- **Architecture Traceability**: Editing Interface
- **Security Traceability**: SEC-OUT-1 (editor format), SEC-OUT-4 (content display)

## Scope

- **Applies To**: Web Client
- **Components**: Editing Interface, Content Store
- **Interfaces / Operations**: Rich-text widgets and every list, form and preview view of the CMS
- **Actors**: Editors (Nina, Ives)
- **Preconditions**: REQ-EDIT-020, REQ-EDIT-030, REQ-EDIT-040
- **Data Classification**: Internal
- **Personal or Regulated Data**: Personal Data (editor GitHub identities)
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Confidentiality, Integrity
- **Control Layers**: Output Encoding, Sanitization
- **Threat References**: STRIDE Elevation of Privilege / Information Disclosure: SECURITY.md Threat Model v2 T-5, T-45; CWE-79
- **Abuse / Misuse Case**: An item containing `<img src=x onerror=…>` is opened by another editor to run script and read the token
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
- **Other**: OWASP XSS Prevention Cheat Sheet (`REF-XSS`)
- **Mapping Basis**: Other: `REF-XSS` as cited in SECURITY.md.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given the Text body editor, when it is opened, then only paragraph, h2, h3, bold, italic, link, list, block quote and Content Store image controls are offered; other rich-text fields offer the same without images.
2. **AC-02 — Boundary or failure behavior**: Given content committed directly with `<script>`, event-handler attributes and `javascript:` links, when every list, form and preview view showing it is opened, then no script runs.
3. **AC-03 — Prohibited behavior**: Given plain-text fields (e.g. materials), when they are edited, then no rich-text controls are offered.

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
- **Integration Tests**: Open hostile fixture items in every view of the test instance with a script-execution canary
- **Security Tests**: Hostile corpus as REQ-OUTPUT-010; canary that reports any execution
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:EDIT-060/AC-01`; AC-02 → `test:EDIT-060/AC-02`; AC-03 → `test:EDIT-060/AC-03` (test command TO BE DECIDED by the first coding PR, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test Sveltia CMS instance at the pinned release (REQ-EDIT-010) with the project configuration, working on a test content repository with a test editor account (not `dalini/nina-content`); Chrome, Edge, Firefox and Safari in their last two major versions; keyboard-only check

## Dependencies

- **Upstream Requirements**: REQ-EDIT-020 ({{ISSUE_URL:REQ-EDIT-020}}), REQ-EDIT-030 ({{ISSUE_URL:REQ-EDIT-030}}), REQ-EDIT-040 ({{ISSUE_URL:REQ-EDIT-040}})
- **Downstream Requirements**: None
- **External Dependencies**: GitHub API; Sveltia CMS (pinned release)
- **Dependency Assumptions**: Sveltia CMS commits through the GitHub API as the signed-in editor (ARCHITECTURE.md Editing Interface)
- **Failure Impact**: Editing stops while GitHub is down; the public site stays online (T-10)

## Implementation Notes

- **Constraints**: Sveltia CMS configuration kept in `dalini/nina-site` (ARCHITECTURE.md Editing Interface); content stays portable (DR-4); DEP-1 to DEP-8 apply
- **Prohibited Approaches**: Rules enforced only in the Editing Interface without the Site Build recheck (DR-3); configuration that lets editors write outside content and media (DR-8)
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and REQUIREMENTS.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; NOT VERIFIED, see ISSUE_PLAN.md Planning Rules), because: Security verification across every view of a third-party UI, with configuration to restrict formats.
- **Required Human Review**: Product review by Nina (forms and wording); security review by Ives
- **Open Decisions**: None
