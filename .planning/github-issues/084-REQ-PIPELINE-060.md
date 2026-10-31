## Metadata

- **ID**: REQ-PIPELINE-060
- **Title**: Build a preview including unpublished content and deploy it to preview.nina-laaf.de with its own preview deploy key
- **Version**: 0.3.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-CICD-3, SEC-SECRET-2, SEC-INPUT-4; REQUIREMENTS.md FR-10.9; ARCHITECTURE.md Site Build (Production and preview); parent {{ISSUE_URL:REQ-PIPELINE-000}}

## Requirement

- **Statement**: The pipeline MUST build a preview of the same Content Store version including unpublished items and deploy it only to `preview.nina-laaf.de`, with a separate preview deploy key and account that can write only the preview web root; the production key MUST NOT be used for the preview, and the preview output MUST NOT reach `nina-laaf.de`.
- **Rationale**: Editors check unpublished content before publishing (FR-10.9) without exposing it (T-21).
- **Assumptions**: None
- **Out of Scope**: The preview sign-in (REQ-PREVIEW-010); limiting the secrets to `main` (REQ-PIPELINE-050); what the preview deploy key may do over SSH (REQ-PIPELINE-140); keeping the Google tag out of the preview output (REQ-CONSENT-040).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build (preview build, separate preview deploy key); Static Hosting
- **Security Traceability**: SEC-CICD-3, SEC-SECRET-2 (preview key part), SEC-INPUT-4

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build pipeline (GitHub Actions in `dalini/nina-site`), Static Hosting
- **Interfaces / Operations**: Preview build and deploy steps; preview deploy account
- **Actors**: build/deploy identity; maintainer (Ives)
- **Preconditions**: REQ-PIPELINE-010, REQ-CONTENT-040, REQ-PIPELINE-050
- **Data Classification**: Confidential
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Confidentiality
- **Control Layers**: Authorization, Data Protection
- **Threat References**: STRIDE Information Disclosure: SECURITY.md Threat Model v3 T-20, T-21; CWE-200
- **Abuse / Misuse Case**: The preview output (with unpublished works) is deployed to the public web root by mistake or with the wrong key
- **Trust Boundary**: TB-4 (Content Store ↔ Site Build ↔ Static Hosting)
- **Untrusted Inputs or Assertions**: Content Store checkout
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

1. **AC-01 — Expected behavior**: Given an unpublished work in the test content repository, when the pipeline runs, then the preview host serves its page and the public host does not.
2. **AC-02 — Boundary or failure behavior**: Given the preview deploy key, when it tries to write to the public web root, then it is denied.
3. **AC-03 — Prohibited behavior**: Given the workflow, when it is reviewed, then the preview steps never receive the production deploy key and the production steps never deploy preview output.

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
- **Integration Tests**: End-to-end run with unpublished fixtures against test public and preview roots
- **Security Tests**: Cross-root write attempts with each key
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:PIPELINE-060/AC-01`; AC-02 → `test:PIPELINE-060/AC-02`; AC-03 → `test:PIPELINE-060/AC-03` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test copy of the workflow in a test repository, a test content repository and a test target host with test keys; never the production keys

## Dependencies

- **Upstream Requirements**: REQ-PIPELINE-010 ({{ISSUE_URL:REQ-PIPELINE-010}}), REQ-CONTENT-040 ({{ISSUE_URL:REQ-CONTENT-040}}), REQ-PIPELINE-050 ({{ISSUE_URL:REQ-PIPELINE-050}})
- **Downstream Requirements**: REQ-PREVIEW-010 ({{ISSUE_URL:REQ-PREVIEW-010}}), REQ-PIPELINE-140 ({{ISSUE_URL:REQ-PIPELINE-140}}), REQ-CONSENT-040 ({{ISSUE_URL:REQ-CONSENT-040}})
- **External Dependencies**: GitHub Actions
- **Dependency Assumptions**: GitHub Actions runs workflows as configured
- **Failure Impact**: A pipeline failure delays publishing; the previous site stays online

## Implementation Notes

- **Constraints**: GitHub Actions in `dalini/nina-site`, Hugo, rsync over SSH (ARCHITECTURE.md Deployment model); secrets come from the `production` environment of `dalini/nina-site`, limited to `main` (REQ-PIPELINE-050); DEP-1 to DEP-8 apply
- **Prohibited Approaches**: Printing secrets; giving content-processing steps access to deploy keys or the Instagram token (SEC-CICD-6)
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Key separation between two outputs from one run is easy to get subtly wrong (T-20, T-21).
- **Required Human Review**: Security review by Ives
- **Open Decisions**: PQ-62 (non-blocking): whether the preview shows the consent banner without a tag loader, or leaves the consent script out.
