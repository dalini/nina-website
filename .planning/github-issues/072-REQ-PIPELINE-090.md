## Metadata

- **ID**: REQ-PIPELINE-090
- **Title**: Process Content Store files without access to the deploy keys or the Instagram token
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-CICD-6, SEC-SECRET-2, `REF-CICD`, `REF-SECRETS`; ARCHITECTURE.md Site Build (Stages); parent {{ISSUE_URL:REQ-PIPELINE-000}}

## Requirement

- **Statement**: Steps that read or process Content Store files (checkout, Hugo, image and PDF processing, content checks) MUST run without access to the production and preview deploy keys and the Instagram token; those secrets MUST be available only to the step that downloads Instagram posts and to the step that deploys the finished output.
- **Rationale**: A parser or generator flaw triggered by content must not reach the secrets (T-54, T-18, T-50).
- **Assumptions**: None
- **Out of Scope**: Where the secrets are stored (blocked scope, PQ-31).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build (Stages)
- **Security Traceability**: SEC-CICD-6

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build pipeline (GitHub Actions in `dalini/nina-site`), Static Hosting
- **Interfaces / Operations**: Job and step layout of the workflow; artifacts passed between jobs
- **Actors**: build/deploy identity; maintainer (Ives)
- **Preconditions**: REQ-PIPELINE-010
- **Data Classification**: Confidential
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Confidentiality
- **Control Layers**: Architecture, Supply Chain
- **Threat References**: STRIDE Elevation of Privilege: SECURITY.md Threat Model v2 T-54, T-18, T-50; CWE-250
- **Abuse / Misuse Case**: A crafted image exploits the image processor and reads the deploy key from the environment
- **Trust Boundary**: TB-4 (Content Store ↔ Site Build ↔ Static Hosting)
- **Untrusted Inputs or Assertions**: All Content Store files
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

1. **AC-01 — Expected behavior**: Given the workflow, when it runs, then content processing runs in a job that holds only the read-only content credential, and the built output reaches the deploy job as an artifact.
2. **AC-02 — Boundary or failure behavior**: Given a test step in the content-processing job that lists its environment and readable files, when it runs, then no deploy key or Instagram token is found.
3. **AC-03 — Prohibited behavior**: Given the deploy job, when it is reviewed, then it does not run Hugo, image tools or content checks; and the Instagram step does not parse Content Store files.

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
- **Integration Tests**: Workflow run with an environment-dump test step in each job
- **Security Tests**: Secret presence check per job; configuration review
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:PIPELINE-090/AC-01`; AC-02 → `test:PIPELINE-090/AC-02`; AC-03 → `test:PIPELINE-090/AC-03` (test command TO BE DECIDED by the first coding PR, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test copy of the workflow in a test repository, a test content repository and a test target host with test keys; never the production keys

## Dependencies

- **Upstream Requirements**: REQ-PIPELINE-010 ({{ISSUE_URL:REQ-PIPELINE-010}})
- **Downstream Requirements**: REQ-INSTAGRAM-030 ({{ISSUE_URL:REQ-INSTAGRAM-030}})
- **External Dependencies**: GitHub Actions
- **Dependency Assumptions**: GitHub Actions runs workflows as configured
- **Failure Impact**: A pipeline failure delays publishing; the previous site stays online

## Implementation Notes

- **Constraints**: GitHub Actions in `dalini/nina-site`, Hugo, rsync over SSH (ARCHITECTURE.md Deployment model); the `production` environment limited to `main` is blocked scope (PQ-31), so secrets are referenced by name and the run is not pointed at production until PQ-31 is answered; DEP-1 to DEP-8 apply
- **Prohibited Approaches**: Printing secrets; giving content-processing steps access to deploy keys or the Instagram token (SEC-CICD-6)
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; NOT VERIFIED, see ISSUE_PLAN.md Planning Rules), because: Job isolation for secrets in GitHub Actions has subtle leaks (shared workspaces, artifacts, environment inheritance).
- **Required Human Review**: Security review by Ives
- **Open Decisions**: PQ-31 (blocking for secret storage only).
