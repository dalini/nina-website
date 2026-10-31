## Metadata

- **ID**: REQ-EDIT-030
- **Title**: Let editors manage Exhibition entries and News items, including linked works and CV settings, with required-field checks
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-4.1 to FR-4.7, FR-5.4, FR-10.1, FR-10.2, FR-10.6; ARCHITECTURE.md Editing Interface; parent {{ISSUE_URL:REQ-EDIT-000}}

## Requirement

- **Statement**: The Editing Interface MUST let editors create, edit, unpublish and delete Exhibition entries (with title, type, venue, city, start and end date, co-exhibitors, external link, linked works, description, installation images with their order, alt text, caption and photo credit, CV hide flag and CV wording) and News items (with title, date, text, external link and image), and MUST reject a save of an Exhibition entry without title, type, venue, city, start date or end date, or whose end date is before its start date (naming the end date), or of a News item without title or date, naming the missing field.
- **Rationale**: Nina keeps exhibitions and news current herself (FR-10.1).
- **Assumptions**: None
- **Out of Scope**: How the slug of an entry is filled in, and duplicate slugs (blocked: PQ-57); upload checks (REQ-EDIT-050).
- **Design Traceability**: N/A
- **Architecture Traceability**: Editing Interface; Content Store (Exhibition entry, News item)
- **Security Traceability**: SEC-OUT-2 (link fields rechecked by the Site Build, REQ-OUTPUT-030)

## Scope

- **Applies To**: Web Client
- **Components**: Editing Interface, Content Store
- **Interfaces / Operations**: Exhibitions and News collections in the CMS
- **Actors**: Editors (Nina, Ives)
- **Preconditions**: REQ-EDIT-010, REQ-CONTENT-050
- **Data Classification**: Internal
- **Personal or Regulated Data**: Personal Data (editor GitHub identities)
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Business-Rule Validation
- **Threat References**: STRIDE Tampering: SECURITY.md Threat Model v3 T-12, T-49 (the Site Build rechecks every rule)
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

1. **AC-01 — Expected behavior**: Given a signed-in test editor, when she creates an Exhibition entry of type group with dates and two linked works and saves, then a commit adds it in the documented model.
2. **AC-02 — Boundary or failure behavior**: Given an Exhibition entry without title, type, venue, city, start date or end date, or a News item without a date, when she saves, then the save is rejected and the form names the field.
3. **AC-03 — Boundary or failure behavior**: Given the type field, when she opens it, then she can choose only solo, two-person, group or fair.
4. **AC-04 — Boundary or failure behavior**: Given an entry with start 2026-06-30 and end 2026-06-01, when she saves, then the save is rejected and the form names the end date.
5. **AC-05 — Prohibited behavior**: Given a News item form, when it is used, then it offers no exhibition type and is saved as a News item only.
6. **AC-06 — Additional criterion**: Given an entry with a German and English description and two installation images with alt text, when she saves, then the commit holds them in the documented model in the order she set.

## Failure Behavior

- **On Invalid Input**: The save is rejected and the form names the missing or invalid field in text (FR-10.6, DESIGN.md Inputs and form feedback); nothing is committed
- **On Authentication Failure**: Editing actions require a signed-in editor (SEC-AUTHN-1); the sign-in through the OAuth helper is REQ-AUTHN-010 and REQ-AUTHN-020
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
- **Acceptance-Criteria Traceability**: AC-01 → `test:EDIT-030/AC-01`; AC-02 → `test:EDIT-030/AC-02`; AC-03 → `test:EDIT-030/AC-03`; AC-04 → `test:EDIT-030/AC-04`; AC-05 → `test:EDIT-030/AC-05`; AC-06 → `test:EDIT-030/AC-06` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test Sveltia CMS instance at the pinned release (REQ-EDIT-010) with the project configuration, working on a test content repository with a test editor account (not `dalini/nina-content`); Chrome, Edge, Firefox and Safari in their last two major versions; keyboard-only check

## Dependencies

- **Upstream Requirements**: REQ-EDIT-010 ({{ISSUE_URL:REQ-EDIT-010}}), REQ-CONTENT-050 ({{ISSUE_URL:REQ-CONTENT-050}})
- **Downstream Requirements**: REQ-EDIT-060 ({{ISSUE_URL:REQ-EDIT-060}}), REQ-EDIT-080 ({{ISSUE_URL:REQ-EDIT-080}})
- **External Dependencies**: GitHub API; Sveltia CMS (pinned release)
- **Dependency Assumptions**: Sveltia CMS commits through the GitHub API as the signed-in editor (ARCHITECTURE.md Editing Interface)
- **Failure Impact**: Editing stops while GitHub is down; the public site stays online (T-10)

## Implementation Notes

- **Constraints**: Sveltia CMS configuration kept in `dalini/nina-site` (ARCHITECTURE.md Editing Interface); content stays portable (DR-4); DEP-1 to DEP-8 apply
- **Prohibited Approaches**: Rules enforced only in the Editing Interface without the Site Build recheck (DR-3); configuration that lets editors write outside content and media (DR-8)
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and REQUIREMENTS.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Declarative CMS configuration following the documented model.
- **Required Human Review**: Product review by Nina (forms and wording); security review by Ives
- **Open Decisions**: PQ-57 (blocking) for the excluded slug part.
