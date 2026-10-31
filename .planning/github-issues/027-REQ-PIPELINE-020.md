## Metadata

- **ID**: REQ-PIPELINE-020
- **Title**: Deploy only the Site Build's generated output
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-CICD-5; parent {{ISSUE_URL:REQ-PIPELINE-000}}

## Requirement

- **Statement**: The deploy MUST upload only the Site Build's generated output, so that repository metadata, source content and configuration are not reachable on the site.
- **Rationale**: Uploaded `.git` data, source content or configuration would expose unpublished content, history and settings (SECURITY.md T-29).
- **Assumptions**: None (the Content Store is the private Git repository `dalini/nina-content`, ARCHITECTURE.md)
- **Out of Scope**: Unpublished items inside the generated output (REQ-CONTENT-040).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build → Static Hosting (DF-6)
- **Security Traceability**: SEC-CICD-5

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build, Static Hosting
- **Interfaces / Operations**: Deploy job; post-deploy check
- **Actors**: Build/deploy identity; maintainer (Ives); Site Build
- **Preconditions**: REQ-PIPELINE-010
- **Data Classification**: Restricted (deploy credentials); Public (output)
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Confidentiality
- **Control Layers**: Architecture, Data Protection
- **Threat References**: STRIDE Information Disclosure: SECURITY.md Threat Model v1 T-29; CWE-538
- **Abuse / Misuse Case**: A visitor requests `/.git/config`, a source content file or the build configuration by URL.
- **Trust Boundary**: TB-4 (Content Store ↔ Site Build ↔ Static Hosting pipeline)
- **Untrusted Inputs or Assertions**: Contents of the working directory at deploy time
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

1. **AC-01 — Expected behavior**: Given a production deploy, when the deployed file list is compared with the build output directory, then they are identical.
2. **AC-02 — Boundary or failure behavior**: Given a deployed site, when `/.git/`, `/.git/config`, a source content path and the build configuration path are requested, then each returns not found.
3. **AC-03 — Prohibited behavior**: Given the deploy job, when its upload source is inspected, then it is the output directory only, never the repository root.

## Failure Behavior

- **On Invalid Input**: If the output directory is missing or empty, the deploy job fails without uploading
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication (SECURITY.md SEC-AUTHN-1 to -3) is decomposed in the step 9 re-run
- **On Authorization Failure**: Deploy attempts by any other identity or branch are rejected by the platform; nothing is deployed
- **On Security-Decision Failure**: Post-deploy check failure marks the deploy as failed and is reported
- **On External Dependency Failure**: N/A
- **On System Error**: The pipeline stops; the previously deployed site stays online
- **Logging / Audit**: Pipeline log: run identifier, trigger, branch, Content Store version, result; no secrets (SEC-SECRET-1)
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: N/A
- **Integration Tests**: Post-deploy HTTP check for the listed paths
- **Security Tests**: Request list of sensitive paths (`/.git/`, content source, config, lockfiles) after each deploy
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:PIPELINE-020/AC-01`; AC-02 → `test:PIPELINE-020/AC-02`; AC-03 → `test:PIPELINE-020/AC-03`
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
- **Prohibited Approaches**: Uploading the repository root and relying on ignore rules alone
- **Implementation Guidance**: Point the upload at the generator's output directory explicitly.
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`), because: Point the upload at the output directory and add a post-deploy path check.
- **Required Human Review**: Security review of the CI/CD configuration
- **Open Decisions**: None (step 10: Hugo; GitHub Actions and the VPS).
