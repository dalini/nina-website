## Metadata

- **ID**: REQ-PIPELINE-080
- **Title**: Start builds only through workflow_dispatch without inputs and a nightly schedule, in one concurrency group
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-CICD-4, `REF-CICD`; REQUIREMENTS.md FR-10.4, FR-4.1, FR-2.3; ARCHITECTURE.md Site Build (When it runs); parent https://github.com/dalini/nina-website/issues/14

## Requirement

- **Statement**: The build workflow MUST have exactly two triggers: `workflow_dispatch` without inputs, and a nightly schedule that runs after midnight Europe/Berlin; it MUST run in one concurrency group so that a new run replaces a queued one; no other trigger (push, `repository_dispatch`, pull request, webhook) may exist.
- **Rationale**: The nightly build keeps FR-4.1 and FR-2.3 correct without editor action (FR-10.4); no other path may start builds (T-22, T-55).
- **Assumptions**: None
- **Out of Scope**: The VPS content check that calls `workflow_dispatch` (REQ-PIPELINE-130).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build (When it runs)
- **Security Traceability**: SEC-CICD-4 (workflow side)

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build pipeline (GitHub Actions in `dalini/nina-site`), Static Hosting
- **Interfaces / Operations**: Workflow `on:` and `concurrency:` configuration
- **Actors**: build/deploy identity; maintainer (Ives)
- **Preconditions**: REQ-PIPELINE-010
- **Data Classification**: Confidential
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity, Availability
- **Control Layers**: Supply Chain
- **Threat References**: STRIDE Spoofing / Tampering: SECURITY.md Threat Model v3 T-22, T-55, T-56; CWE-78
- **Abuse / Misuse Case**: Someone with the trigger token passes a crafted input to inject a shell command into a run that later holds the deploy key
- **Trust Boundary**: TB-4 (Content Store ↔ Site Build ↔ Static Hosting)
- **Untrusted Inputs or Assertions**: The dispatch call
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

1. **AC-01 — Expected behavior**: Given a `workflow_dispatch` call, when it is received, then a build runs; given the nightly schedule time, a build runs and uses the Europe/Berlin date.
2. **AC-02 — Boundary or failure behavior**: Given a run in progress and two more dispatches, when they arrive, then only the latest waits and the earlier queued one is replaced.
3. **AC-03 — Boundary or failure behavior**: Given a dispatch call that sends inputs, when it is made, then the workflow accepts none and no input value reaches any step.
4. **AC-04 — Prohibited behavior**: Given the workflow, when its triggers are reviewed, then no push, pull request, `repository_dispatch` or other trigger exists.

## Failure Behavior

- **On Invalid Input**: The run fails before any deploy step; nothing is published
- **On Authentication Failure**: N/A: the pipeline authenticates with its own credentials only
- **On Authorization Failure**: A step without the needed permission fails; the run stops and publishes nothing
- **On Security-Decision Failure**: Deny by default: a missing or failed check stops the run before deploy
- **On External Dependency Failure**: GitHub Actions unavailable: no build runs and the previous site stays online
- **On System Error**: The run fails and publishes nothing; the previous site stays online
- **Logging / Audit**: The run log records steps and results; secrets are masked and never printed (SEC-SECRET-1)
- **Alerting**: A failed run emails Ives (REQ-PIPELINE-110)

## Test Strategy

- **Unit Tests**: N/A unless the issue adds first-party scripts; those get unit tests
- **Integration Tests**: Dispatch and schedule runs in a test repository; concurrent dispatches
- **Security Tests**: Trigger review; dispatch with crafted inputs
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:PIPELINE-080/AC-01`; AC-02 → `test:PIPELINE-080/AC-02`; AC-03 → `test:PIPELINE-080/AC-03`; AC-04 → `test:PIPELINE-080/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test copy of the workflow in a test repository, a test content repository and a test target host with test keys; never the production keys

## Dependencies

- **Upstream Requirements**: REQ-PIPELINE-010 (https://github.com/dalini/nina-website/issues/85)
- **Downstream Requirements**: REQ-PIPELINE-130 (https://github.com/dalini/nina-website/issues/94)
- **External Dependencies**: GitHub Actions
- **Dependency Assumptions**: GitHub Actions runs workflows as configured
- **Failure Impact**: A pipeline failure delays publishing; the previous site stays online

## Implementation Notes

- **Constraints**: GitHub Actions in `dalini/nina-site`, Hugo, rsync over SSH (ARCHITECTURE.md Deployment model); secrets come from the `production` environment of `dalini/nina-site`, limited to `main` (REQ-PIPELINE-050); DEP-1 to DEP-8 apply
- **Prohibited Approaches**: Printing secrets; giving content-processing steps access to deploy keys or the Instagram token (SEC-CICD-6)
- **Implementation Guidance**: GitHub schedules use UTC; a time such as 00:30 UTC is after midnight in Berlin in both winter and summer time.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Workflow trigger configuration fully specified by SEC-CICD-4.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
