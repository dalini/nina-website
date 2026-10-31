## Metadata

- **ID**: REQ-PIPELINE-060
- **Title**: Keep previews of unpublished content from being publicly reachable
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Repository maintainer (dalini); the maintainer role is still open in SECURITY.md SQ-10
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-CICD-3; parent {{ISSUE_URL:REQ-PIPELINE-000}}

## Requirement

- **Statement**: If the hosting or CI produces previews of unpublished content, they MUST be reachable only by authenticated editors; otherwise previews MUST be turned off.
- **Rationale**: Public preview URLs would expose drafts (SECURITY.md T-21).
- **Assumptions**: ARCHITECTURE.md ASSUMPTION: the Content Store is a version-controlled repository (AQ-5)
- **Out of Scope**: Editor authentication for previews (SEC-AUTHN-1; blocked by PQ-2): until PQ-2 is answered, the only option that meets SEC-CICD-3 here is previews turned off.
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build pipeline, Static Hosting (TB-4 → TB-1)
- **Security Traceability**: SEC-CICD-3

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build, Static Hosting
- **Interfaces / Operations**: Preview settings of the hosting/CI platform
- **Actors**: Build/deploy identity; maintainer (SQ-10); Site Build
- **Preconditions**: REQ-PIPELINE-010
- **Data Classification**: Internal (unpublished content)
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Confidentiality
- **Control Layers**: Architecture, Authorization
- **Threat References**: STRIDE Information Disclosure: SECURITY.md Threat Model v1 T-21
- **Abuse / Misuse Case**: Someone finds a preview URL of a draft and reads unpublished content.
- **Trust Boundary**: TB-4 (Content Store ↔ Site Build ↔ Static Hosting pipeline)
- **Untrusted Inputs or Assertions**: Requests to preview URLs
- **Authoritative Enforcement Point**: CI/CD platform configuration and Static Hosting
- **Independent Verification**: Branch protection and CI permissions are enforced by the platform, not by the build script
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: TO BE DECIDED: SECURITY.md cites ASVS 5.0.0 but uses no ASVS identifiers until they are verified; assurance level open in SECURITY.md SQ-9
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: TO BE DECIDED: not assessed
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP CI/CD Security Cheat Sheet (`REF-CICD`)
- **Mapping Basis**: Other: `REF-CICD` as cited in SECURITY.md.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given the hosting and CI configuration, when it is reviewed, then preview deployments are turned off.
2. **AC-02 — Boundary or failure behavior**: Given a change on a non-main branch, when the pipeline runs, then no preview URL is created.
3. **AC-03 — Prohibited behavior**: Given any preview URL the platform reports, when it is requested without signing in, then access is denied.

## Failure Behavior

- **On Invalid Input**: N/A
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication is blocked scope (ISSUE_PLAN.md PQ-2)
- **On Authorization Failure**: Deploy attempts by any other identity or branch are rejected by the platform; nothing is deployed
- **On Security-Decision Failure**: If preview status cannot be determined, treat previews as on and fail the review
- **On External Dependency Failure**: N/A
- **On System Error**: The pipeline stops; the previously deployed site stays online
- **Logging / Audit**: Pipeline log: run identifier, trigger, branch, Content Store version, result; no secrets (SEC-SECRET-1)
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: N/A
- **Integration Tests**: Branch build creates no preview; request to a known preview pattern is denied
- **Security Tests**: Unauthenticated request to preview URLs
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:PIPELINE-060/AC-01`; AC-02 → `test:PIPELINE-060/AC-02`; AC-03 → `test:PIPELINE-060/AC-03`
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
- **Prohibited Approaches**: Unlisted but public preview URLs as a protection
- **Implementation Guidance**: Revisit when PQ-2 is answered if editors need previews.
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`), because: Turn previews off and verify; one configuration change.
- **Required Human Review**: Security review of the CI/CD configuration
- **Open Decisions**: Non-blocking: PQ-17 (whether the provider has previews).
