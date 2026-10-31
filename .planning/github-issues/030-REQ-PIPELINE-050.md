## Metadata

- **ID**: REQ-PIPELINE-050
- **Title**: Make deploy and Instagram secrets available only to production builds of the main branch
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Repository maintainer (dalini); the maintainer role is still open in SECURITY.md SQ-10
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: Critical
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-SECRET-2; parent {{ISSUE_URL:REQ-PIPELINE-000}}

## Requirement

- **Statement**: Deploy and Instagram secrets MUST be available only to the production build of the Content Store's main branch and MUST NOT be available to builds of other branches, previews or external contributions.
- **Rationale**: Unreviewed branches or contributions must not read secrets (SECURITY.md T-20).
- **Assumptions**: ARCHITECTURE.md ASSUMPTION: the Content Store is a version-controlled repository (AQ-5)
- **Out of Scope**: The Instagram secret itself (PQ-7); how previews work (REQ-PIPELINE-060).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build pipeline (TB-4)
- **Security Traceability**: SEC-SECRET-2

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build, Static Hosting
- **Interfaces / Operations**: CI secret scoping; branch and environment protection
- **Actors**: Build/deploy identity; maintainer (SQ-10); Site Build
- **Preconditions**: REQ-PIPELINE-010
- **Data Classification**: Restricted (deploy credentials); Public (output)
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Confidentiality
- **Control Layers**: Authorization, Supply Chain
- **Threat References**: STRIDE Information Disclosure: SECURITY.md Threat Model v1 T-20
- **Abuse / Misuse Case**: An editing-system draft branch or an external pull request runs a build step that prints or sends the deploy secret.
- **Trust Boundary**: TB-4 (Content Store ↔ Site Build ↔ Static Hosting pipeline)
- **Untrusted Inputs or Assertions**: Builds of non-main branches, previews and external contributions
- **Authoritative Enforcement Point**: CI/CD platform configuration and Static Hosting
- **Independent Verification**: Branch protection and CI permissions are enforced by the platform, not by the build script
- **Zero Trust Relevance**: TO BE DECIDED: per-request least privilege per NIST SP 800-207 tenets is relevant; section not verified

## Standards Alignment

- **OWASP ASVS 5.0.0**: TO BE DECIDED: SECURITY.md cites ASVS 5.0.0 but uses no ASVS identifiers until they are verified; assurance level open in SECURITY.md SQ-9
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: TO BE DECIDED: not assessed
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP CI/CD Security and Secrets Management Cheat Sheets (`REF-CICD`, `REF-SECRETS`)
- **Mapping Basis**: Other: `REF-CICD`, `REF-SECRETS` as cited in SECURITY.md.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given a production build of main, when it requests the deploy secret, then the secret is available.
2. **AC-02 — Boundary or failure behavior**: Given a build of another branch that tries to read the deploy secret, when it runs, then the secret is empty or the job is denied access, and the job cannot deploy.
3. **AC-03 — Prohibited behavior**: Given an external contribution (fork pull request), when its checks run, then no deploy or Instagram secret is exposed to it.

## Failure Behavior

- **On Invalid Input**: N/A
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication is blocked scope (ISSUE_PLAN.md PQ-2)
- **On Authorization Failure**: Deploy attempts by any other identity or branch are rejected by the platform; nothing is deployed
- **On Security-Decision Failure**: Unclear scope: secrets are not exposed (deny by default)
- **On External Dependency Failure**: N/A
- **On System Error**: The pipeline stops; the previously deployed site stays online
- **Logging / Audit**: Pipeline log: run identifier, trigger, branch, Content Store version, result; no secrets (SEC-SECRET-1)
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: N/A
- **Integration Tests**: Test build on a non-main branch attempting to read the secret
- **Security Tests**: Fork pull request check run without secrets
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:PIPELINE-050/AC-01`; AC-02 → `test:PIPELINE-050/AC-02`; AC-03 → `test:PIPELINE-050/AC-03`
- **Coverage Target**: Project coverage target TO BE DECIDED (build and test commands are TO BE DECIDED in CLAUDE.md). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: CI/CD platform and Static Hosting (TO BE DECIDED: PQ-17, ARCHITECTURE.md AQ-4) with a test branch and a test deploy target

## Dependencies

- **Upstream Requirements**: REQ-PIPELINE-010 ({{ISSUE_URL:REQ-PIPELINE-010}})
- **Downstream Requirements**: None
- **External Dependencies**: CI/CD platform and hosting provider (PQ-17)
- **Dependency Assumptions**: The platform enforces branch protection, job permissions and secret scoping as configured
- **Failure Impact**: A platform outage stops publishing; the live site stays as it is (ARCHITECTURE.md DR-1)

## Implementation Notes

- **Constraints**: DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Repository-wide secrets visible to every workflow; running untrusted contributions with privileged triggers
- **Implementation Guidance**: Use a protected production environment bound to main if the platform offers one.
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`), because: Secret scoping against fork and branch triggers is easy to get subtly wrong on CI platforms (T-20).
- **Required Human Review**: Security review of the CI/CD configuration
- **Open Decisions**: Non-blocking: PQ-17.
