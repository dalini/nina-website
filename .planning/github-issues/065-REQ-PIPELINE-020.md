## Metadata

- **ID**: REQ-PIPELINE-020
- **Title**: Deploy only the Site Build's generated output
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-CICD-5, `REF-CICD`; parent {{ISSUE_URL:REQ-PIPELINE-000}}

## Requirement

- **Statement**: Only the Site Build's generated output MUST be deployed to Static Hosting; repository metadata, source content and configuration MUST NOT be reachable on the site.
- **Rationale**: A `.git` directory or source content on the server exposes history and unpublished items (T-29).
- **Assumptions**: None
- **Out of Scope**: Excluding unpublished items from the output itself (REQ-CONTENT-040).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build; Static Hosting
- **Security Traceability**: SEC-CICD-5

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build pipeline (GitHub Actions in `dalini/nina-site`), Static Hosting
- **Interfaces / Operations**: rsync source path; post-deploy check
- **Actors**: build/deploy identity; maintainer (Ives)
- **Preconditions**: REQ-PIPELINE-010
- **Data Classification**: Confidential
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Confidentiality
- **Control Layers**: Data Protection
- **Threat References**: STRIDE Information Disclosure: SECURITY.md Threat Model v2 T-29; CWE-538
- **Abuse / Misuse Case**: A visitor requests `/.git/config` or a source Markdown path
- **Trust Boundary**: TB-4 (Content Store ↔ Site Build ↔ Static Hosting)
- **Untrusted Inputs or Assertions**: N/A: the step uploads only build output
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

1. **AC-01 — Expected behavior**: Given a deploy, when it completes, then the web root contains only files from the build output directory.
2. **AC-02 — Boundary or failure behavior**: Given a post-deploy check, when it requests `/.git/`, `/.github/`, source content paths, `hugo.toml` and the CMS configuration path, then each returns 404.
3. **AC-03 — Prohibited behavior**: Given the rsync invocation, when it is reviewed, then its source is the output directory only.

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
- **Integration Tests**: Post-deploy path check against the test host
- **Security Tests**: Request list of known sensitive paths
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:PIPELINE-020/AC-01`; AC-02 → `test:PIPELINE-020/AC-02`; AC-03 → `test:PIPELINE-020/AC-03` (test command `deno task test`, CLAUDE.md)
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
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; NOT VERIFIED, see ISSUE_PLAN.md Planning Rules), because: Point the upload at the output directory and add a post-deploy path check.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
