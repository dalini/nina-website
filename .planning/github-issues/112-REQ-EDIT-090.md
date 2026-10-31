## Metadata

- **ID**: REQ-EDIT-090
- **Title**: Show Nina in the Editing Interface when her changes are not live yet and which item and field failed
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-10.8 (editor side), FR-10.4; SECURITY.md SEC-OUT-4 (status shown as text), SEC-CICD-9 (public status file), SEC-HTTP-4; ARCHITECTURE.md Editing Interface (shows the publish status banner), DF-9; T-24, T-58, T-60; parent {{ISSUE_URL:REQ-EDIT-000}}

## Requirement

- **Statement**: The Editing Interface MUST show Nina that her changes are not live yet when the publish status file reports a failure, naming the failing item and field when content caused it, and when a change she saved is still not live 15 minutes after it was saved, by comparing the content version in the status file with the latest commit on `main` of the Content Store and its time; it MUST show the status file's values as text only.
- **Rationale**: A failed or never-started publish must not go unnoticed by Nina (T-24, T-58); the status file is untrusted input on the editing origin (T-60).
- **Assumptions**: None
- **Out of Scope**: Writing the status file (REQ-PIPELINE-120); the failure email to Ives (REQ-PIPELINE-110); restyling Sveltia CMS (not done, DESIGN.md Components).
- **Design Traceability**: DESIGN.md Components (Inputs and form feedback applies to the public site only; the Sveltia CMS UI is used as is). No DESIGN.md component defines this banner: PQ-63
- **Architecture Traceability**: Editing Interface (reads the publish status file, DF-9, and shows the banner); Content Store (latest commit through the GitHub API); DR-1 (status carries no content)
- **Security Traceability**: SEC-OUT-4, SEC-CICD-9, SEC-HTTP-4 (script from its own origin, connections to its own origin and GitHub)

## Scope

- **Applies To**: Web Client
- **Components**: Editing Interface, Content Store
- **Interfaces / Operations**: Status banner on the Editing Interface page; request for the publish status file; GitHub API read of the latest commit on `main` with the signed-in editor's token
- **Actors**: Editors (Nina, Ives)
- **Preconditions**: REQ-PIPELINE-120, REQ-EDIT-010, REQ-HTTP-050
- **Data Classification**: Internal
- **Personal or Regulated Data**: Personal Data (editor GitHub identities)
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity, Availability
- **Control Layers**: Output Encoding, Logging and Monitoring
- **Threat References**: STRIDE Denial of Service and Tampering: SECURITY.md Threat Model v3 T-24, T-58, T-60, T-45; CWE-79
- **Abuse / Misuse Case**: Someone who can write the status file puts markup in it to run script with the editor's token
- **Trust Boundary**: TB-2 (editor browser ↔ Editing Interface); DF-9
- **Untrusted Inputs or Assertions**: The publish status file; GitHub API responses
- **Authoritative Enforcement Point**: The banner script on `edit.nina-laaf.de` (text-only display); the Site Build's status writer (REQ-PIPELINE-120, fields)
- **Independent Verification**: Browser tests with hostile status files and stale versions
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP XSS Prevention Cheat Sheet (`REF-XSS`)
- **Mapping Basis**: Other: `REF-XSS` as cited in SECURITY.md SEC-OUT-4.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given the status file shows `success` for the latest commit on `main`, when Nina opens the Editing Interface, then no warning banner is shown.
2. **AC-02 — Boundary or failure behavior**: Given the latest build failed on a work's missing year, when Nina opens or uses the Editing Interface, then a banner tells her that her changes are not live yet and names the work's identifier and the field `year`.
3. **AC-03 — Boundary or failure behavior**: Given Nina saved a change 16 minutes ago and the status file still names an older content version, when the Editing Interface checks the status, then the banner tells her that the change is not live yet, even though no build failed.
4. **AC-04 — Prohibited behavior**: Given a status file whose fields contain `<script>alert(1)</script>`, when the banner shows it, then it appears as text and no script runs (SEC-OUT-4).

## Failure Behavior

- **On Invalid Input**: A status file that cannot be parsed is shown as "status unknown" text and treated as not live
- **On Authentication Failure**: Editing actions require a signed-in editor (SEC-AUTHN-1); the sign-in through the OAuth helper is REQ-AUTHN-010 and REQ-AUTHN-020
- **On Authorization Failure**: A commit by an account that is not on the Content Store allow-list is rejected by GitHub (SEC-AUTHZ-1, REQ-STORE-010); the Editing Interface shows the error and nothing changes
- **On Security-Decision Failure**: Unknown status counts as not live; nothing in the status file is rendered as HTML
- **On External Dependency Failure**: GitHub API unavailable: the Editing Interface shows an error; nothing is half-written; the public site stays online (T-10)
- **On System Error**: If the check fails, the banner says the status could not be checked; editing still works
- **Logging / Audit**: N/A: no server-side component
- **Alerting**: N/A: Ives is emailed by REQ-PIPELINE-110

## Test Strategy

- **Unit Tests**: Decision logic: success, failure with item and field, stale version older than 15 minutes, unparsable file
- **Integration Tests**: Test Sveltia CMS instance with a test status file and a test content repository
- **Security Tests**: Status file with markup; CSP violation check under REQ-HTTP-050
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:EDIT-090/AC-01`; AC-02 → `test:EDIT-090/AC-02`; AC-03 → `test:EDIT-090/AC-03`; AC-04 → `test:EDIT-090/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test Sveltia CMS instance at the pinned release (REQ-EDIT-010) with the project configuration, working on a test content repository with a test editor account (not `dalini/nina-content`); Chrome, Edge, Firefox and Safari in their last two major versions; keyboard-only check

## Dependencies

- **Upstream Requirements**: REQ-PIPELINE-120 ({{ISSUE_URL:REQ-PIPELINE-120}}), REQ-EDIT-010 ({{ISSUE_URL:REQ-EDIT-010}}), REQ-HTTP-050 ({{ISSUE_URL:REQ-HTTP-050}})
- **Downstream Requirements**: None
- **External Dependencies**: GitHub API (latest commit of `main`)
- **Dependency Assumptions**: The signed-in editor's token can read `main` of the Content Store (SEC-AUTHZ-1)
- **Failure Impact**: If GitHub is unreachable the not-live check cannot run; the banner says so

## Implementation Notes

- **Constraints**: First-party script file served from `edit.nina-laaf.de` under the CSP of REQ-HTTP-050; Sveltia CMS UI not restyled; DEP-1 to DEP-8 apply
- **Prohibited Approaches**: Inserting status values as HTML; reading content values for the banner; changing the CMS bundle itself
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Browser code on the editing origin that reads the public status file and the editor's own latest save, must show untrusted text safely, and runs where editor tokens live (T-45, T-60).
- **Required Human Review**: Product review by Nina (forms and wording); security review by Ives
- **Open Decisions**: PQ-63 (non-blocking): the banner's wording and appearance.
