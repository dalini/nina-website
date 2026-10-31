## Metadata

- **ID**: REQ-PIPELINE-040
- **Title**: Keep credentials out of the repositories, the generated output, client code and logs, with secret scanning on every change
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-SECRET-1, `REF-SECRETS`; parent {{ISSUE_URL:REQ-PIPELINE-000}}

## Requirement

- **Statement**: Deploy credentials, editing-system credentials and the Instagram token MUST NOT appear in `dalini/nina-site`, `dalini/nina-content`, the generated output, client code or logs; every change to `dalini/nina-site` and every Content Store checkout and build output MUST be scanned for secrets, and a finding MUST stop the run before deploy.
- **Rationale**: A leaked key or token gives an attacker the site or the account (T-43, T-62).
- **Assumptions**: None
- **Out of Scope**: Where the secrets are stored (production environment: blocked scope PQ-31; root-only files on the VPS: blocked scope PQ-33); yearly rotation by Ives (operational, recorded in the SEC-HOST-1 review).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build pipeline; Static Hosting
- **Security Traceability**: SEC-SECRET-1 (hygiene part)

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build pipeline (GitHub Actions in `dalini/nina-site`), Static Hosting
- **Interfaces / Operations**: Secret scanning step; log masking
- **Actors**: build/deploy identity; maintainer (Ives)
- **Preconditions**: REQ-PIPELINE-010
- **Data Classification**: Confidential
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Confidentiality
- **Control Layers**: Data Protection, Logging and Monitoring
- **Threat References**: STRIDE Information Disclosure: SECURITY.md Threat Model v2 T-43, T-62; CWE-798, CWE-532
- **Abuse / Misuse Case**: A token is pasted into a content field or printed in a debug step
- **Trust Boundary**: TB-4 (Content Store ↔ Site Build ↔ Static Hosting)
- **Untrusted Inputs or Assertions**: Every change and the Content Store checkout
- **Authoritative Enforcement Point**: Site Build pipeline configuration in `dalini/nina-site` (maintainer-only, DR-8)
- **Independent Verification**: Configuration review by Ives and a test run that tries the prohibited path
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP Secrets Management Cheat Sheet (`REF-SECRETS`)
- **Mapping Basis**: Other: `REF-SECRETS` as cited in SECURITY.md.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given a change to `dalini/nina-site`, when CI runs, then a secret scan runs and passes on clean changes.
2. **AC-02 — Boundary or failure behavior**: Given a test secret value committed to the test content repository, when the build runs, then the scan reports it and the deploy step does not run.
3. **AC-03 — Prohibited behavior**: Given a full run, when its logs and output are searched for the test secrets' values, then none is found.

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
- **Integration Tests**: Run with planted test secrets in a code change, in content and in the output
- **Security Tests**: Log and output search for secret values; masking check
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:PIPELINE-040/AC-01`; AC-02 → `test:PIPELINE-040/AC-02`; AC-03 → `test:PIPELINE-040/AC-03` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test copy of the workflow in a test repository, a test content repository and a test target host with test keys; never the production keys

## Dependencies

- **Upstream Requirements**: REQ-PIPELINE-010 ({{ISSUE_URL:REQ-PIPELINE-010}})
- **Downstream Requirements**: None
- **External Dependencies**: GitHub Actions
- **Dependency Assumptions**: GitHub Actions runs workflows as configured
- **Failure Impact**: A pipeline failure delays publishing; the previous site stays online

## Implementation Notes

- **Constraints**: GitHub Actions in `dalini/nina-site`, Hugo, rsync over SSH (ARCHITECTURE.md Deployment model); the `production` environment limited to `main` (PQ-31 answered in step 10 again: paid plan) is drafted by the step 9 re-run; until then secrets are referenced by name and the run is not pointed at production; DEP-1 to DEP-8 apply
- **Prohibited Approaches**: Printing secrets; giving content-processing steps access to deploy keys or the Instagram token (SEC-CICD-6)
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; NOT VERIFIED, see ISSUE_PLAN.md Planning Rules), because: Secret scanning, masking and a fail-closed gate with platform features.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None. Answered in step 10 again (`.planning/OPEN_QUESTIONS.md`): PQ-31: paid GitHub plan (Pro or Team): branch protection and the `production` environment limited to `main` are available; PQ-33: each secret file is owned by the one unprivileged account that uses it; the Instagram token belongs to the deploy account, which also runs the refresh (SEC-SECRET-1). Parts that were excluded as blocked scope are drafted by the step 9 re-run.
