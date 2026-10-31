## Metadata

- **ID**: REQ-STORE-010
- **Title**: Keep dalini/nina-content private, writable only by Nina and Ives with individual 2FA accounts, with Ives as sole admin and EDITORS.md as the record
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-AUTHZ-1, SEC-AUTHZ-2, SEC-AUTHZ-4, SEC-AUTHN-2; REQUIREMENTS.md FR-10.5; ARCHITECTURE.md Repositories, Content Store; parent {{ISSUE_URL:REQ-STORE-000}}

## Requirement

- **Statement**: `dalini/nina-content` MUST NOT be publicly readable; it MUST accept changes only from its collaborators Nina and Ives, each with an individual GitHub account with a second factor enabled; Ives MUST be its only admin; every editor addition or removal MUST be decided by the owners, carried out by Ives and committed as a line in `EDITORS.md` in the Content Store.
- **Rationale**: The store holds unpublished content and originals (T-13); only listed editors may change it (T-6, T-12).
- **Assumptions**: None
- **Out of Scope**: Branch protection and verified commits (decided in step 10 again; drafted by the step 9 re-run, PQ-31); the read-only build credential (REQ-PIPELINE-010); the VPS content check and backup credentials (decided in step 10 again; drafted by the step 9 re-run, PQ-33).
- **Design Traceability**: N/A
- **Architecture Traceability**: Content Store (`dalini/nina-content`, private)
- **Security Traceability**: SEC-AUTHZ-1, SEC-AUTHZ-2, SEC-AUTHZ-4, SEC-AUTHN-2 (individual accounts with second factor)

## Scope

- **Applies To**: Multiple
- **Components**: Content Store
- **Interfaces / Operations**: Repository visibility, collaborators and roles; `EDITORS.md`
- **Actors**: Maintainer (Ives); owners (Nina, Ives); editors
- **Preconditions**: None
- **Data Classification**: Internal
- **Personal or Regulated Data**: Personal Data (editor account names)
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Authorization, Confidentiality
- **Control Layers**: Authorization, Authentication
- **Threat References**: STRIDE Elevation of Privilege / Information Disclosure / Tampering: SECURITY.md Threat Model v2 T-1, T-6, T-12, T-13; CWE-284
- **Abuse / Misuse Case**: A non-listed account pushes to the store, or an anonymous visitor tries to read it
- **Trust Boundary**: TB-3 (Editing Interface ↔ Content Store)
- **Untrusted Inputs or Assertions**: Requests from any GitHub account or anonymous client
- **Authoritative Enforcement Point**: GitHub repository settings, changed only by Ives
- **Independent Verification**: Test with an account that is not on the list, plus a settings review
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: N/A
- **Mapping Basis**: Repository settings; no external standard mapping beyond ASVS Level 1.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given the repository, when its settings are reviewed, then it is private, its collaborators are exactly Nina and Ives with write access, Ives is the only admin, and `EDITORS.md` lists both.
2. **AC-02 — Boundary or failure behavior**: Given a test account that is not a collaborator, when it tries to push or open a write through the API, then GitHub rejects it and nothing changes.
3. **AC-03 — Boundary or failure behavior**: Given an unauthenticated request for the repository or a raw file, when it is made, then it is rejected.
4. **AC-04 — Prohibited behavior**: Given the collaborator list, when it is compared with `EDITORS.md` and its history, then no collaborator change exists without a matching `EDITORS.md` line, and no shared or role account is listed.
5. **AC-05 — Additional criterion**: Given each collaborator account, when the review is done, then GitHub shows a second factor enabled for it.

## Failure Behavior

- **On Invalid Input**: N/A
- **On Authentication Failure**: GitHub rejects unauthenticated requests; nothing is disclosed beyond GitHub's own not-found response
- **On Authorization Failure**: GitHub rejects the operation; the repository and the site stay unchanged
- **On Security-Decision Failure**: Deny by default: anything not explicitly granted stays denied
- **On External Dependency Failure**: GitHub unavailable: no change is possible; the public site stays online (T-10)
- **On System Error**: N/A: settings only
- **Logging / Audit**: GitHub's own audit of collaborator and settings changes; the `EDITORS.md` line for editor changes (SEC-AUTHZ-2)
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: N/A: settings, no first-party code
- **Integration Tests**: Push and read attempts with a non-listed test account and unauthenticated
- **Security Tests**: Settings review; collaborator list against `EDITORS.md`
- **Compliance Tests / Evidence**: Evidence: dated review of visibility, collaborators, admin role and second factors
- **Acceptance-Criteria Traceability**: AC-01 → `test:STORE-010/AC-01`; AC-02 → `test:STORE-010/AC-02`; AC-03 → `test:STORE-010/AC-03`; AC-04 → `test:STORE-010/AC-04`; AC-05 → `test:STORE-010/AC-05` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test GitHub account that is not a collaborator; the real repositories for the settings review

## Dependencies

- **Upstream Requirements**: None
- **Downstream Requirements**: REQ-PIPELINE-010 ({{ISSUE_URL:REQ-PIPELINE-010}})
- **External Dependencies**: GitHub
- **Dependency Assumptions**: GitHub enforces repository permissions as documented
- **Failure Impact**: A GitHub outage stops editing and building; the public site stays online

## Implementation Notes

- **Constraints**: GitHub repositories `dalini/nina-content` and `dalini/nina-site` (ARCHITECTURE.md Repositories)
- **Prohibited Approaches**: Shared accounts; granting rights to anyone not named in SECURITY.md
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; settings changes are made by Ives, who records them; a human reviews every change. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; NOT VERIFIED, see ISSUE_PLAN.md Planning Rules), because: Repository settings and a record file, fully specified by SEC-AUTHZ-1, -2 and -4.
- **Required Human Review**: Security review by Ives; Nina confirms the editor list as owner
- **Open Decisions**: None
