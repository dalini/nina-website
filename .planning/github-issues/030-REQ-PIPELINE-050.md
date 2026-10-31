## Metadata

- **ID**: REQ-PIPELINE-050
- **Title**: Make deploy and Instagram secrets available only to production builds of the main branch
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: Critical
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-SECRET-2; parent {{ISSUE_URL:REQ-PIPELINE-000}}

## Requirement

- **Statement**: The production deploy key and the Instagram token MUST be available only to the production build of the Content Store's `main` branch and MUST NOT be available to builds of other branches or external contributions; the preview build MUST use its own preview deploy key, which can write only the preview site.
- **Rationale**: Unreviewed branches or contributions must not read secrets (SECURITY.md T-20).
- **Assumptions**: None (the Content Store is the private Git repository `dalini/nina-content`, ARCHITECTURE.md)
- **Out of Scope**: How previews are built and protected (REQ-PIPELINE-060).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build pipeline (TB-4)
- **Security Traceability**: SEC-SECRET-2

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build, Static Hosting
- **Interfaces / Operations**: CI secret scoping; branch and environment protection
- **Actors**: Build/deploy identity; maintainer (Ives); Site Build
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
- **Zero Trust Relevance**: Not applicable (SECURITY.md Security assurance target)

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
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
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication (SECURITY.md SEC-AUTHN-1 to -3) is decomposed in the step 9 re-run
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
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: GitHub Actions in `dalini/nina-site` and the VPS with nginx (ARCHITECTURE.md) with a test branch and a test deploy target

## Dependencies

- **Upstream Requirements**: REQ-PIPELINE-010 ({{ISSUE_URL:REQ-PIPELINE-010}})
- **Downstream Requirements**: None
- **External Dependencies**: GitHub Actions in `dalini/nina-site`; VPS with nginx (ARCHITECTURE.md)
- **Dependency Assumptions**: The platform enforces branch protection, job permissions and secret scoping as configured
- **Failure Impact**: A platform outage stops publishing; the live site stays as it is (ARCHITECTURE.md DR-1)

## Implementation Notes

- **Constraints**: DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Repository-wide secrets visible to every workflow; running untrusted contributions with privileged triggers
- **Implementation Guidance**: Use a protected production environment bound to main if the platform offers one.
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`), because: Secret scoping against fork and branch triggers is easy to get subtly wrong on CI platforms (T-20).
- **Required Human Review**: Security review of the CI/CD configuration
- **Open Decisions**: None (step 10: GitHub Actions environment `production`; SECURITY.md SEC-SECRET-2).
