## Metadata

- **ID**: REQ-PIPELINE-100
- **Title**: Set a time limit on every build job, keep the Actions spending limit at zero and send usage alerts to Ives
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Operational
- **Source / Parent**: SECURITY.md SEC-CICD-8, `REF-CICD`; ARCHITECTURE.md Site Build (Cost); parent {{ISSUE_URL:REQ-PIPELINE-000}}

## Requirement

- **Statement**: Every build job MUST have a time limit well above a normal build; the account's Actions spending limit MUST stay at zero and GitHub's included-usage alerts for Actions minutes MUST be on and reach Ives; an unexplained build run or usage alert is handled as a suspected leak of the build-trigger token.
- **Rationale**: A stuck build or a leaked trigger token must not exhaust the 2,000 included minutes or cause costs (T-56).
- **Assumptions**: None
- **Out of Scope**: Rotating the trigger token (blocked scope with the content check, PQ-33).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build (one concurrency group; cost note)
- **Security Traceability**: SEC-CICD-8

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build pipeline, GitHub account settings
- **Interfaces / Operations**: `timeout-minutes` of every job; account billing and notification settings
- **Actors**: build/deploy identity; maintainer (Ives)
- **Preconditions**: REQ-PIPELINE-010
- **Data Classification**: Confidential
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Availability
- **Control Layers**: Availability, Logging and Monitoring
- **Threat References**: STRIDE Denial of Service: SECURITY.md Threat Model v2 T-56; CWE-400
- **Abuse / Misuse Case**: A leaked trigger token starts builds in a loop
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

1. **AC-01 — Expected behavior**: Given the workflow, when it is reviewed, then every job has `timeout-minutes` set.
2. **AC-02 — Boundary or failure behavior**: Given a test job that sleeps past its limit, when it runs, then GitHub cancels it at the limit.
3. **AC-03 — Prohibited behavior**: Given the account billing settings, when they are reviewed, then the Actions spending limit is zero and usage alerts reach Ives.

## Failure Behavior

- **On Invalid Input**: The run fails before any deploy step; nothing is published
- **On Authentication Failure**: N/A: the pipeline authenticates with its own credentials only
- **On Authorization Failure**: A step without the needed permission fails; the run stops and publishes nothing
- **On Security-Decision Failure**: Deny by default: a missing or failed check stops the run before deploy
- **On External Dependency Failure**: GitHub Actions unavailable: no build runs and the previous site stays online
- **On System Error**: The run fails and publishes nothing; the previous site stays online
- **Logging / Audit**: The run log records steps and results; secrets are masked and never printed (SEC-SECRET-1)
- **Alerting**: GitHub included-usage alerts for Actions minutes to Ives

## Test Strategy

- **Unit Tests**: N/A unless the issue adds first-party scripts; those get unit tests
- **Integration Tests**: Over-long test job in a test repository
- **Security Tests**: Billing and alert settings review (also in the yearly REQ-HOST-010 review)
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:PIPELINE-100/AC-01`; AC-02 → `test:PIPELINE-100/AC-02`; AC-03 → `test:PIPELINE-100/AC-03` (test command TO BE DECIDED by the first coding PR, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test copy of the workflow in a test repository, a test content repository and a test target host with test keys; never the production keys

## Dependencies

- **Upstream Requirements**: REQ-PIPELINE-010 ({{ISSUE_URL:REQ-PIPELINE-010}})
- **Downstream Requirements**: None
- **External Dependencies**: GitHub Actions
- **Dependency Assumptions**: GitHub Actions runs workflows as configured
- **Failure Impact**: A pipeline failure delays publishing; the previous site stays online

## Implementation Notes

- **Constraints**: GitHub Actions in `dalini/nina-site`, Hugo, rsync over SSH (ARCHITECTURE.md Deployment model); the `production` environment limited to `main` is blocked scope (PQ-31), so secrets are referenced by name and the run is not pointed at production until PQ-31 is answered; DEP-1 to DEP-8 apply
- **Prohibited Approaches**: Printing secrets; giving content-processing steps access to deploy keys or the Instagram token (SEC-CICD-6)
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; NOT VERIFIED, see ISSUE_PLAN.md Planning Rules), because: Configuration and settings with a simple test.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: PQ-43 (non-blocking): the time limit value.
