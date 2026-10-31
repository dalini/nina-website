## Metadata

- **ID**: REQ-STORE-030
- **Title**: Limit write access to dalini/nina-site to the maintainer
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: ARCHITECTURE.md Repositories, DR-8; SECURITY.md SEC-AUTHZ-3, `REF-CICD`; parent {{ISSUE_URL:REQ-STORE-000}}

## Requirement

- **Statement**: Only Ives (the maintainer) MUST have write access to `dalini/nina-site`; Nina's editor account MUST NOT be able to change templates, build configuration or pipeline definitions.
- **Rationale**: Templates and pipeline run with the deploy secrets; editors must not be able to change them (T-11).
- **Assumptions**: None
- **Out of Scope**: The test with the Editing Interface token issued to Nina (REQ-AUTHN-010); the accepted default OAuth App scope and Ives's own token (SEC-AUTHZ-3 accepted risks).
- **Design Traceability**: N/A
- **Architecture Traceability**: DR-8 (templates, design tokens, build configuration and pipeline in `dalini/nina-site`, maintainer only)
- **Security Traceability**: SEC-AUTHZ-3 (repository access part)

## Scope

- **Applies To**: Multiple
- **Components**: Site Build (`dalini/nina-site`)
- **Interfaces / Operations**: Collaborators and roles of `dalini/nina-site`
- **Actors**: Maintainer (Ives); owners (Nina, Ives); editors
- **Preconditions**: None
- **Data Classification**: Internal
- **Personal or Regulated Data**: Personal Data (editor account names)
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Authorization
- **Control Layers**: Authorization
- **Threat References**: STRIDE Elevation of Privilege: SECURITY.md Threat Model v3 T-11; CWE-284
- **Abuse / Misuse Case**: A stolen editor account changes a template to inject script or exfiltrate deploy keys
- **Trust Boundary**: TB-3 (Editing Interface ↔ Content Store)
- **Untrusted Inputs or Assertions**: Pushes from any account other than Ives's
- **Authoritative Enforcement Point**: GitHub repository settings, changed only by Ives
- **Independent Verification**: Test with an account that is not on the list, plus a settings review
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP CI/CD Security Cheat Sheet (`REF-CICD`)
- **Mapping Basis**: Other: `REF-CICD` as cited in SECURITY.md.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given the repository settings, when they are reviewed, then Ives is the only account with write or admin access.
2. **AC-02 — Boundary or failure behavior**: Given Nina's account (or a test account with her role), when it tries to push to `dalini/nina-site`, then GitHub rejects it.
3. **AC-03 — Prohibited behavior**: Given the collaborator list, when it is reviewed, then no editor-only account, team or deploy key has write access.

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
- **Integration Tests**: Push attempt with a test account in Nina's role
- **Security Tests**: Settings review including deploy keys
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:STORE-030/AC-01`; AC-02 → `test:STORE-030/AC-02`; AC-03 → `test:STORE-030/AC-03` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test GitHub account that is not a collaborator; the real repositories for the settings review

## Dependencies

- **Upstream Requirements**: None
- **Downstream Requirements**: REQ-EDIT-010 ({{ISSUE_URL:REQ-EDIT-010}})
- **External Dependencies**: GitHub
- **Dependency Assumptions**: GitHub enforces repository permissions as documented
- **Failure Impact**: A GitHub outage stops editing and building; the public site stays online

## Implementation Notes

- **Constraints**: GitHub repositories `dalini/nina-content` and `dalini/nina-site` (ARCHITECTURE.md Repositories)
- **Prohibited Approaches**: Shared accounts; granting rights to anyone not named in SECURITY.md
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; settings changes are made by Ives, who records them; a human reviews every change. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Repository settings with one negative test.
- **Required Human Review**: Security review by Ives; Nina confirms the editor list as owner
- **Open Decisions**: None
