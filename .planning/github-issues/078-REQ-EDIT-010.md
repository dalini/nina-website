## Metadata

- **ID**: REQ-EDIT-010
- **Title**: Serve a pinned, verified Sveltia CMS release and the project configuration from edit.nina-laaf.de, never from a CDN
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-CICD-2, DEP-3 to DEP-5, DEP-7, `REF-SUPPLY`; ARCHITECTURE.md Editing Interface, DR-9; parent {{ISSUE_URL:REQ-EDIT-000}}

## Requirement

- **Statement**: The Editing Interface page on `edit.nina-laaf.de` MUST load the Sveltia CMS bundle only from its own origin, pinned to an exact release and verified against a published checksum or the lockfile, together with the project's CMS configuration from `dalini/nina-site`; it MUST NOT load the bundle or any script from a CDN.
- **Rationale**: The CMS runs with the editor's GitHub token; a CDN or floating release could ship code that steals it (T-46).
- **Assumptions**: None
- **Out of Scope**: The OAuth helper (blocked scope, PQ-34); the CSP (REQ-HTTP-050).
- **Design Traceability**: N/A
- **Architecture Traceability**: Editing Interface (bundle served from the VPS at a pinned release); DR-9
- **Security Traceability**: SEC-CICD-2 (Editing Interface bundle)

## Scope

- **Applies To**: Web Client
- **Components**: Editing Interface, Content Store
- **Interfaces / Operations**: `edit.nina-laaf.de` index page; bundle file; CMS configuration file
- **Actors**: Editors (Nina, Ives)
- **Preconditions**: REQ-HTTP-040, REQ-STORE-030
- **Data Classification**: Internal
- **Personal or Regulated Data**: Personal Data (editor GitHub identities)
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Supply Chain
- **Threat References**: STRIDE Tampering: SECURITY.md Threat Model v2 T-46; CWE-494, CWE-829
- **Abuse / Misuse Case**: A compromised CDN or release serves a bundle that sends the token in browser storage elsewhere
- **Trust Boundary**: TB-2 (editor browser ↔ Editing Interface), TB-3 (Editing Interface ↔ Content Store)
- **Untrusted Inputs or Assertions**: The Sveltia CMS release artifact
- **Authoritative Enforcement Point**: Editing Interface (first check); Site Build (authoritative recheck, DR-3)
- **Independent Verification**: The Site Build rechecks the rules (REQ-CONTENT-030)
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP Software Supply Chain Security Cheat Sheet (`REF-SUPPLY`)
- **Mapping Basis**: Other: `REF-SUPPLY` as cited in SECURITY.md.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given the Editing Interface page, when it loads, then its script comes only from `https://edit.nina-laaf.de` and the CMS starts with the project configuration.
2. **AC-02 — Boundary or failure behavior**: Given a downloaded bundle whose checksum differs from the published or locked value, when installation runs, then it is refused.
3. **AC-03 — Prohibited behavior**: Given the page and the configuration, when they are reviewed, then no CDN URL and no unpinned version reference exists.
4. **AC-04 — Additional criterion**: Given a new Sveltia CMS security release, when Ives follows the documented update procedure, then the pinned version and checksum are updated and verified in one reviewed change.

## Failure Behavior

- **On Invalid Input**: A bundle whose checksum does not match is not installed; the previous bundle stays in place
- **On Authentication Failure**: Editing actions require a signed-in editor (SEC-AUTHN-1); the production sign-in through the OAuth helper is blocked scope (PQ-34) and not part of this issue
- **On Authorization Failure**: A commit by an account that is not on the Content Store allow-list is rejected by GitHub (SEC-AUTHZ-1, REQ-STORE-010); the Editing Interface shows the error and nothing changes
- **On Security-Decision Failure**: N/A: the authoritative checks are the Site Build rechecks
- **On External Dependency Failure**: GitHub API unavailable: the save fails with an error shown to the editor; nothing is half-written; the public site stays online (T-10)
- **On System Error**: A failed save leaves the Content Store unchanged and keeps the editor's input on screen
- **Logging / Audit**: Each save is a commit in the Content Store with the signed-in editor and time (FR-10.7)
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: N/A unless an install script is added
- **Integration Tests**: Load the test `edit.` host and record script origins
- **Security Tests**: Checksum mismatch fixture; network recording of the page
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:EDIT-010/AC-01`; AC-02 → `test:EDIT-010/AC-02`; AC-03 → `test:EDIT-010/AC-03`; AC-04 → `test:EDIT-010/AC-04` (test command TO BE DECIDED by the first coding PR, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test Sveltia CMS instance at the pinned release (REQ-EDIT-010) with the project configuration, working on a test content repository with a test editor account (not `dalini/nina-content`); Chrome, Edge, Firefox and Safari in their last two major versions; keyboard-only check

## Dependencies

- **Upstream Requirements**: REQ-HTTP-040 ({{ISSUE_URL:REQ-HTTP-040}}), REQ-STORE-030 ({{ISSUE_URL:REQ-STORE-030}})
- **Downstream Requirements**: REQ-HTTP-050 ({{ISSUE_URL:REQ-HTTP-050}}), REQ-EDIT-020 ({{ISSUE_URL:REQ-EDIT-020}}), REQ-EDIT-030 ({{ISSUE_URL:REQ-EDIT-030}}), REQ-EDIT-040 ({{ISSUE_URL:REQ-EDIT-040}}), REQ-EDIT-070 ({{ISSUE_URL:REQ-EDIT-070}})
- **External Dependencies**: GitHub API; Sveltia CMS (pinned release)
- **Dependency Assumptions**: Sveltia CMS commits through the GitHub API as the signed-in editor (ARCHITECTURE.md Editing Interface)
- **Failure Impact**: Editing stops while GitHub is down; the public site stays online (T-10)

## Implementation Notes

- **Constraints**: Installed on the VPS by the maintainer: the deploy account has no write access to the Editing Interface files (SEC-CICD-1 lists no such path); configuration in `dalini/nina-site`; DEP-1 to DEP-8 apply
- **Prohibited Approaches**: Rules enforced only in the Editing Interface without the Site Build recheck (DR-3); configuration that lets editors write outside content and media (DR-8)
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and REQUIREMENTS.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; NOT VERIFIED, see ISSUE_PLAN.md Planning Rules), because: Supply-chain control for code that runs with every editor's token.
- **Required Human Review**: Product review by Nina (forms and wording); security review by Ives
- **Open Decisions**: None
