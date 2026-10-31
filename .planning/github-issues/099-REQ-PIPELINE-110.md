## Metadata

- **ID**: REQ-PIPELINE-110
- **Title**: Email Ives when a build fails
- **Version**: 0.3.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 fourth run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Reliability
- **Source / Parent**: REQUIREMENTS.md FR-10.8; ARCHITECTURE.md Site Build (Outputs: a GitHub Actions failure email to the maintainer); parent {{ISSUE_URL:REQ-PIPELINE-000}}

## Requirement

- **Statement**: Every failed run of the build workflow, whether started by `workflow_dispatch` or by the schedule, MUST notify Ives by email.
- **Rationale**: Ives must learn of failed publishes (FR-10.8, T-24).
- **Assumptions**: None
- **Out of Scope**: Showing the status to Nina and detecting a change that is not live 15 minutes after saving (REQ-EDIT-090); alerts from the VPS content check, the token refresh and the certificate renewal, which go through the local mail relay agent (REQ-PIPELINE-130, REQ-INSTAGRAM-050, REQ-HTTP-060, REQ-HOST-040).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build (GitHub Actions failure email to the maintainer)
- **Security Traceability**: N/A

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build pipeline (GitHub Actions in `dalini/nina-site`), Static Hosting
- **Interfaces / Operations**: GitHub Actions notification settings
- **Actors**: build/deploy identity; maintainer (Ives)
- **Preconditions**: REQ-PIPELINE-010
- **Data Classification**: Confidential
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Availability
- **Control Layers**: Logging and Monitoring
- **Threat References**: STRIDE Denial of Service: SECURITY.md Threat Model v3 T-24
- **Abuse / Misuse Case**: N/A
- **Trust Boundary**: TB-4 (Content Store ↔ Site Build ↔ Static Hosting)
- **Untrusted Inputs or Assertions**: N/A
- **Authoritative Enforcement Point**: Site Build pipeline configuration in `dalini/nina-site` (maintainer-only, DR-8)
- **Independent Verification**: Configuration review by Ives and a test run that tries the prohibited path
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP CI/CD Security Cheat Sheet (`REF-CICD`); NIST SP 800-218 SSDF 1.1 (`REF-SSDF`)
- **Mapping Basis**: Other: `REF-CICD` and `REF-SSDF` as cited in SECURITY.md; no practice IDs verified.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given a dispatch-started run fails, when it ends, then Ives receives a failure email.
2. **AC-02 — Expected behavior**: Given a scheduled run fails, when it ends, then Ives receives a failure email.
3. **AC-03 — Prohibited behavior**: Given the failure email, when it is read, then it contains no secret values.

## Failure Behavior

- **On Invalid Input**: The run fails before any deploy step; nothing is published
- **On Authentication Failure**: N/A: the pipeline authenticates with its own credentials only
- **On Authorization Failure**: A step without the needed permission fails; the run stops and publishes nothing
- **On Security-Decision Failure**: Deny by default: a missing or failed check stops the run before deploy
- **On External Dependency Failure**: GitHub Actions unavailable: no build runs and the previous site stays online
- **On System Error**: The run fails and publishes nothing; the previous site stays online
- **Logging / Audit**: The run log records steps and results; secrets are masked and never printed (SEC-SECRET-1)
- **Alerting**: GitHub Actions failure email to Ives for every failed run

## Test Strategy

- **Unit Tests**: N/A unless the issue adds first-party scripts; those get unit tests
- **Integration Tests**: Forced failures of a dispatched and a scheduled run
- **Security Tests**: N/A
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:PIPELINE-110/AC-01`; AC-02 → `test:PIPELINE-110/AC-02`; AC-03 → `test:PIPELINE-110/AC-03` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test copy of the workflow in a test repository, a test content repository and a test target host with test keys; never the production keys

## Dependencies

- **Upstream Requirements**: REQ-PIPELINE-010 ({{ISSUE_URL:REQ-PIPELINE-010}})
- **Downstream Requirements**: None
- **External Dependencies**: GitHub Actions
- **Dependency Assumptions**: GitHub Actions runs workflows as configured
- **Failure Impact**: A pipeline failure delays publishing; the previous site stays online

## Implementation Notes

- **Constraints**: GitHub Actions in `dalini/nina-site`, Hugo, rsync over SSH (ARCHITECTURE.md Deployment model); secrets come from the `production` environment of `dalini/nina-site`, limited to `main` (REQ-PIPELINE-050); DEP-1 to DEP-8 apply
- **Prohibited Approaches**: Printing secrets; giving content-processing steps access to deploy keys or the Instagram token (SEC-CICD-6)
- **Implementation Guidance**: GitHub sends scheduled-run failures to the user who last changed the schedule and dispatched-run failures to the dispatching account; check both reach Ives.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Notification settings and two forced-failure tests.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
