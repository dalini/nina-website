## Metadata

- **ID**: REQ-PIPELINE-060
- **Title**: Keep previews of unpublished content from being publicly reachable
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-CICD-3; parent {{ISSUE_URL:REQ-PIPELINE-000}}

## Requirement

- **Statement**: Previews of unpublished content MUST exist only at `preview.nina-laaf.de`, deployed with the preview deploy key, and MUST be reachable only by editors signed in with their individual HTTP Basic Auth credentials over HTTPS; the hosting and CI MUST produce no other previews.
- **Rationale**: Public preview URLs would expose drafts (SECURITY.md T-21).
- **Assumptions**: None (the Content Store is the private Git repository `dalini/nina-content`, ARCHITECTURE.md)
- **Out of Scope**: Which content the preview build includes beyond unpublished items (it builds the same `main` content including unpublished items, ARCHITECTURE.md Site Build).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build pipeline, Static Hosting (TB-4 → TB-1)
- **Security Traceability**: SEC-CICD-3

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build, Static Hosting
- **Interfaces / Operations**: Preview settings of the hosting/CI platform
- **Actors**: Build/deploy identity; maintainer (Ives); Site Build
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

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP CI/CD Security Cheat Sheet (`REF-CICD`)
- **Mapping Basis**: Other: `REF-CICD` as cited in SECURITY.md.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given a content change on `main`, when the pipeline runs, then the preview build including unpublished items is deployed to `preview.nina-laaf.de` with the preview deploy key, and an editor with valid credentials can open it.
2. **AC-02 — Boundary or failure behavior**: Given the hosting and CI configuration, when it is reviewed, then no other preview deployment (per branch or per pull request) exists.
3. **AC-03 — Prohibited behavior**: Given any preview URL, when it is requested without credentials or with wrong credentials, then access is denied with no page content, and repeated failures are limited.

## Failure Behavior

- **On Invalid Input**: N/A
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication (SECURITY.md SEC-AUTHN-1 to -3) is decomposed in the step 9 re-run
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
- **Prohibited Approaches**: Unlisted but public preview URLs as a protection
- **Implementation Guidance**: nginx `auth_basic` with one credential per editor; HTTPS and headers as SEC-HTTP-4.
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`), because: Turn previews off and verify; one configuration change.
- **Required Human Review**: Security review of the CI/CD configuration
- **Open Decisions**: None (step 10: protected preview site with per-editor HTTP Basic Auth).
