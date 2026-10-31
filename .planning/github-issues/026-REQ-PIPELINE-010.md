## Metadata

- **ID**: REQ-PIPELINE-010
- **Title**: Deploy to production only from the Content Store main branch after a successful build, with a least-privilege deploy identity
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: Critical
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-CICD-1, SEC-INPUT-3 (previous site stays online); REQUIREMENTS.md FR-10.4 (deploy side); ARCHITECTURE.md Site Build, Static Hosting, DR-1; parent {{ISSUE_URL:REQ-PIPELINE-000}}

## Requirement

- **Statement**: The pipeline MUST deploy to production only builds of the Content Store's main branch that completed successfully, using a deploy identity that holds only the permissions needed to publish the site; a failed build MUST leave the live site unchanged.
- **Rationale**: Only reviewed content reaches production and a compromised or broken build cannot replace the site (SECURITY.md T-19, T-20, T-27).
- **Assumptions**: None (the Content Store is the private Git repository `dalini/nina-content`, ARCHITECTURE.md)
- **Out of Scope**: The 5-minute poll of the Content Store, the nightly build and the 15-minute publish limit (FR-10.4, SEC-CICD-4, ARCHITECTURE.md Site Build): decided in step 10 and decomposed in the step 9 re-run.
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build → Static Hosting (DF-6); DR-1
- **Security Traceability**: SEC-CICD-1, SEC-INPUT-3

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build, Static Hosting
- **Interfaces / Operations**: CI pipeline: build job, deploy job, branch protection
- **Actors**: Build/deploy identity; maintainer (Ives); Site Build
- **Preconditions**: REQ-CONTENT-030 (build fails on invalid content)
- **Data Classification**: Restricted (deploy credentials); Public (output)
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Authorization, Supply Chain
- **Threat References**: STRIDE Tampering / Elevation of Privilege: SECURITY.md Threat Model v1 T-19, T-20, T-27
- **Abuse / Misuse Case**: A branch build or a compromised step deploys altered content or uses broad deploy rights to change hosting settings.
- **Trust Boundary**: TB-4 (Content Store ↔ Site Build ↔ Static Hosting pipeline)
- **Untrusted Inputs or Assertions**: Builds of non-main branches; build outputs of failed runs
- **Authoritative Enforcement Point**: CI/CD platform configuration and Static Hosting
- **Independent Verification**: Branch protection and CI permissions are enforced by the platform, not by the build script
- **Zero Trust Relevance**: Not applicable (SECURITY.md Security assurance target)

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: NIST SP 800-218 SSDF 1.1 (`REF-SSDF`); OWASP CI/CD Security Cheat Sheet (`REF-CICD`)
- **Mapping Basis**: Other: OWASP CI/CD Security Cheat Sheet (`REF-CICD`) and NIST SSDF 1.1 (`REF-SSDF`) as cited in SECURITY.md; no practice IDs verified.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given a change merged to the Content Store's main branch with valid content, when the pipeline runs, then the build succeeds and the deploy job publishes the output to production.
2. **AC-02 — Boundary or failure behavior**: Given a main-branch change with invalid content, when the pipeline runs, then the build fails, the deploy job does not run, and the live site still serves the previous version.
3. **AC-03 — Boundary or failure behavior**: Given a change on any other branch, when the pipeline runs, then no production deploy job runs.
4. **AC-04 — Prohibited behavior**: Given the deploy identity, when its permissions are reviewed, then it can publish site files and nothing else (no hosting account settings, DNS, or repository administration).

## Failure Behavior

- **On Invalid Input**: Build fails; deploy skipped; live site unchanged
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication (SECURITY.md SEC-AUTHN-1 to -3) is decomposed in the step 9 re-run
- **On Authorization Failure**: Deploy attempts by any other identity or branch are rejected by the platform; nothing is deployed
- **On Security-Decision Failure**: If branch or build result cannot be determined, do not deploy
- **On External Dependency Failure**: N/A
- **On System Error**: The pipeline stops; the previously deployed site stays online
- **Logging / Audit**: Pipeline log: run identifier, trigger, branch, Content Store version, result; no secrets (SEC-SECRET-1)
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: N/A: platform configuration
- **Integration Tests**: Pipeline runs on main (deploy), on a feature branch (no deploy) and on main with invalid content (no deploy, live site unchanged)
- **Security Tests**: Attempt a deploy from a non-main branch and with the deploy identity's permissions to change a hosting setting: both rejected
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 to AC-03 → pipeline integration runs; AC-04 → permission review checklist
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: GitHub Actions in `dalini/nina-site` and the VPS with nginx (ARCHITECTURE.md) with a test branch and a test deploy target

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-030 ({{ISSUE_URL:REQ-CONTENT-030}})
- **Downstream Requirements**: REQ-HTTP-010 ({{ISSUE_URL:REQ-HTTP-010}}), REQ-HTTP-020 ({{ISSUE_URL:REQ-HTTP-020}}), REQ-HTTP-030 ({{ISSUE_URL:REQ-HTTP-030}}), REQ-PIPELINE-020 ({{ISSUE_URL:REQ-PIPELINE-020}}), REQ-PIPELINE-030 ({{ISSUE_URL:REQ-PIPELINE-030}}), REQ-PIPELINE-040 ({{ISSUE_URL:REQ-PIPELINE-040}}), REQ-PIPELINE-050 ({{ISSUE_URL:REQ-PIPELINE-050}}), REQ-PIPELINE-060 ({{ISSUE_URL:REQ-PIPELINE-060}}), REQ-PIPELINE-070 ({{ISSUE_URL:REQ-PIPELINE-070}})
- **External Dependencies**: GitHub Actions in `dalini/nina-site`; VPS with nginx (ARCHITECTURE.md)
- **Dependency Assumptions**: The platform enforces branch protection, job permissions and secret scoping as configured
- **Failure Impact**: A platform outage stops publishing; the live site stays as it is (ARCHITECTURE.md DR-1)

## Implementation Notes

- **Constraints**: CI/CD: GitHub Actions in `dalini/nina-site`; hosting: VPS with nginx (ARCHITECTURE.md); Static site generator: Hugo (ARCHITECTURE.md Site Build); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Deploying from a developer machine; a deploy token with account-wide rights
- **Implementation Guidance**: Run REQ-PRIV-010's browser test and the REQ-HTTP header checks as gates in this pipeline once they exist.
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`), because: Least-privilege deploy identity, branch protection and fail-closed deploy gating across the CI platform and host.
- **Required Human Review**: Security review of the CI/CD configuration
- **Open Decisions**: None (step 10: Hugo; GitHub Actions in `dalini/nina-site`; deploy over rsync/SSH to the VPS; content in `dalini/nina-content`; maximum publish delay 15 minutes).
