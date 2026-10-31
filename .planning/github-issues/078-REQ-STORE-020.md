## Metadata

- **ID**: REQ-STORE-020
- **Title**: Disable GitHub Actions in dalini/nina-content and hold no Actions secrets there
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-CICD-7, SEC-CICD-4, `REF-CICD`; parent {{ISSUE_URL:REQ-STORE-000}}

## Requirement

- **Statement**: GitHub Actions MUST be disabled for `dalini/nina-content`, and that repository MUST hold no Actions secrets, so that workflow files committed there never run.
- **Rationale**: A workflow committed by an editor account would be an unplanned build path using the account's minutes (T-51, T-56).
- **Assumptions**: None
- **Out of Scope**: The build workflow in `dalini/nina-site` (REQ-PIPELINE-*).
- **Design Traceability**: N/A
- **Architecture Traceability**: Content Store (TB-3)
- **Security Traceability**: SEC-CICD-7

## Scope

- **Applies To**: Multiple
- **Components**: Content Store
- **Interfaces / Operations**: Actions settings and secrets of `dalini/nina-content`
- **Actors**: Maintainer (Ives); owners (Nina, Ives); editors
- **Preconditions**: None
- **Data Classification**: Internal
- **Personal or Regulated Data**: Personal Data (editor account names)
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Availability, Integrity
- **Control Layers**: Supply Chain
- **Threat References**: STRIDE Denial of Service: SECURITY.md Threat Model v3 T-51, T-56
- **Abuse / Misuse Case**: An editor token commits `.github/workflows/x.yml` to run code on the account's runners
- **Trust Boundary**: TB-3 (Editing Interface ↔ Content Store)
- **Untrusted Inputs or Assertions**: Workflow files in the Content Store
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

1. **AC-01 — Expected behavior**: Given the repository settings, when they are reviewed, then Actions is disabled and the secrets list is empty.
2. **AC-02 — Boundary or failure behavior**: Given a test workflow committed to a branch of the repository, when it would trigger, then no run starts.
3. **AC-03 — Prohibited behavior**: Given the repository, when its settings and environments are reviewed, then no secret, variable or environment with secrets exists.

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
- **Integration Tests**: Commit a harmless test workflow to a branch; confirm no run
- **Security Tests**: Settings review
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:STORE-020/AC-01`; AC-02 → `test:STORE-020/AC-02`; AC-03 → `test:STORE-020/AC-03` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test GitHub account that is not a collaborator; the real repositories for the settings review

## Dependencies

- **Upstream Requirements**: None
- **Downstream Requirements**: None
- **External Dependencies**: GitHub
- **Dependency Assumptions**: GitHub enforces repository permissions as documented
- **Failure Impact**: A GitHub outage stops editing and building; the public site stays online

## Implementation Notes

- **Constraints**: GitHub repositories `dalini/nina-content` and `dalini/nina-site` (ARCHITECTURE.md Repositories)
- **Prohibited Approaches**: Shared accounts; granting rights to anyone not named in SECURITY.md
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; settings changes are made by Ives, who records them; a human reviews every change. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: One settings change and one test.
- **Required Human Review**: Security review by Ives; Nina confirms the editor list as owner
- **Open Decisions**: None
