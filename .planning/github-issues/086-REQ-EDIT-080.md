## Metadata

- **ID**: REQ-EDIT-080
- **Title**: Show who changed each content item and when, and restore an earlier version
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-10.7; SECURITY.md SEC-AUDIT-1; ARCHITECTURE.md Editing Interface (change history and restore through the Git history); parent {{ISSUE_URL:REQ-EDIT-000}}

## Requirement

- **Statement**: For each content item the Editing Interface MUST show who changed it and when, taking the editor from the Content Store history, and MUST let an editor restore an earlier version as a new change.
- **Rationale**: Nina can see and undo changes (FR-10.7, T-9).
- **Assumptions**: None
- **Out of Scope**: Protecting the history against rewriting and requiring verified commits (blocked scope, PQ-31).
- **Design Traceability**: N/A
- **Architecture Traceability**: Editing Interface; Content Store (Git history)
- **Security Traceability**: SEC-AUDIT-1 (attribution taken from the sign-in; enforcement is blocked scope, PQ-31)

## Scope

- **Applies To**: Web Client
- **Components**: Editing Interface, Content Store
- **Interfaces / Operations**: Item history and restore in the CMS
- **Actors**: Editors (Nina, Ives)
- **Preconditions**: REQ-EDIT-020, REQ-EDIT-030, REQ-EDIT-040
- **Data Classification**: Internal
- **Personal or Regulated Data**: Personal Data (editor GitHub identities)
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Accountability
- **Control Layers**: Logging and Monitoring
- **Threat References**: STRIDE Repudiation: SECURITY.md Threat Model v2 T-9
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

1. **AC-01 — Expected behavior**: Given a work changed by Nina and then by Ives, when its history is opened, then both changes are listed with editor and time, newest first.
2. **AC-02 — Boundary or failure behavior**: Given an earlier version is restored, when the restore is saved, then a new commit by the restoring editor brings back that version and the history keeps every intermediate change.
3. **AC-03 — Prohibited behavior**: Given the history view, when an item is restored, then no history entry is removed or rewritten.

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

- **Unit Tests**: N/A unless first-party code is added
- **Integration Tests**: Two test editor accounts making changes; restore through the test instance
- **Security Tests**: Content written directly to the test content repository, bypassing the forms, is rejected by the Site Build recheck
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:EDIT-080/AC-01`; AC-02 → `test:EDIT-080/AC-02`; AC-03 → `test:EDIT-080/AC-03` (test command TO BE DECIDED by the first coding PR, CLAUDE.md)
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
- **AI Development Guidance**: Follow CLAUDE.md and REQUIREMENTS.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; NOT VERIFIED, see ISSUE_PLAN.md Planning Rules), because: Depends on a capability of the editing system that must be verified, with a careful restore path.
- **Required Human Review**: Product review by Nina (forms and wording); security review by Ives
- **Open Decisions**: PQ-44 (non-blocking): verify how Sveltia CMS provides history and restore; a gap is raised as an open question, not filled by the implementer.
