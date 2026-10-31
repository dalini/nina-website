## Metadata

- **ID**: REQ-PIPELINE-010
- **Title**: Deploy to production only from the Content Store main branch after a successful build, with a least-privilege deploy identity
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Repository maintainer (dalini); the maintainer role is still open in SECURITY.md SQ-10
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: Critical
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-CICD-1, SEC-INPUT-3 (previous site stays online); REQUIREMENTS.md FR-10.4 (deploy side); ARCHITECTURE.md Site Build, Static Hosting, DR-1; parent {{ISSUE_URL:REQ-PIPELINE-000}}

## Requirement

- **Statement**: The pipeline MUST deploy to production only builds of the Content Store's main branch that completed successfully, using a deploy identity that holds only the permissions needed to publish the site; a failed build MUST leave the live site unchanged.
- **Rationale**: Only reviewed content reaches production and a compromised or broken build cannot replace the site (SECURITY.md T-19, T-20, T-27).
- **Assumptions**: ARCHITECTURE.md ASSUMPTION: the Content Store is a version-controlled repository (AQ-5)
- **Out of Scope**: Publish triggers from the Editing Interface and their authentication (FR-10.4 save/publish, SEC-CICD-4; blocked by PQ-1); scheduled builds (ARCHITECTURE.md ASSUMPTION, needed for FR-4.1, blocked by PQ-11); maximum publish delay (FR-10.4 TO BE DECIDED).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build → Static Hosting (DF-6); DR-1
- **Security Traceability**: SEC-CICD-1, SEC-INPUT-3

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build, Static Hosting
- **Interfaces / Operations**: CI pipeline: build job, deploy job, branch protection
- **Actors**: Build/deploy identity; maintainer (SQ-10); Site Build
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
- **Zero Trust Relevance**: TO BE DECIDED: least privilege per NIST SP 800-207 tenets is relevant; section not verified

## Standards Alignment

- **OWASP ASVS 5.0.0**: TO BE DECIDED: SECURITY.md cites ASVS 5.0.0 but uses no ASVS identifiers until they are verified; assurance level open in SECURITY.md SQ-9
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: TO BE DECIDED: not assessed
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
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication is blocked scope (ISSUE_PLAN.md PQ-2)
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
- **Coverage Target**: Project coverage target TO BE DECIDED (build and test commands are TO BE DECIDED in CLAUDE.md). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: CI/CD platform and Static Hosting (TO BE DECIDED: PQ-17, ARCHITECTURE.md AQ-4) with a test branch and a test deploy target

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-030 ({{ISSUE_URL:REQ-CONTENT-030}})
- **Downstream Requirements**: REQ-HTTP-010 ({{ISSUE_URL:REQ-HTTP-010}}), REQ-HTTP-020 ({{ISSUE_URL:REQ-HTTP-020}}), REQ-HTTP-030 ({{ISSUE_URL:REQ-HTTP-030}}), REQ-PIPELINE-020 ({{ISSUE_URL:REQ-PIPELINE-020}}), REQ-PIPELINE-030 ({{ISSUE_URL:REQ-PIPELINE-030}}), REQ-PIPELINE-040 ({{ISSUE_URL:REQ-PIPELINE-040}}), REQ-PIPELINE-050 ({{ISSUE_URL:REQ-PIPELINE-050}}), REQ-PIPELINE-060 ({{ISSUE_URL:REQ-PIPELINE-060}}), REQ-PIPELINE-070 ({{ISSUE_URL:REQ-PIPELINE-070}})
- **External Dependencies**: CI/CD platform and hosting provider (PQ-17)
- **Dependency Assumptions**: The platform enforces branch protection, job permissions and secret scoping as configured
- **Failure Impact**: A platform outage stops publishing; the live site stays as it is (ARCHITECTURE.md DR-1)

## Implementation Notes

- **Constraints**: CI/CD platform and hosting: TO BE DECIDED (PQ-17); Static site generator: TO BE DECIDED (PQ-16, ARCHITECTURE.md AQ-1); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Deploying from a developer machine; a deploy token with account-wide rights
- **Implementation Guidance**: Run REQ-PRIV-010's browser test and the REQ-HTTP header checks as gates in this pipeline once they exist.
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`), because: Least-privilege deploy identity, branch protection and fail-closed deploy gating across the CI platform and host.
- **Required Human Review**: Security review of the CI/CD configuration
- **Open Decisions**: Non-blocking: PQ-16, PQ-17, PQ-18. Maximum publish delay (FR-10.4): TO BE DECIDED.
