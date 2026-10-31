## Metadata

- **ID**: REQ-CONTENT-070
- **Title**: Fail the Site Build when the Content Store checkout contains a symbolic link or special file
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-INPUT-7, SEC-CICD-6, `REF-HUGO-ADV`; ARCHITECTURE.md Site Build (Stages); parent https://github.com/dalini/nina-website/issues/3

## Requirement

- **Statement**: The Site Build MUST fail and publish nothing when the Content Store checkout contains a symbolic link or any entry that is not a regular file or directory.
- **Rationale**: A committed symbolic link can make a generator read files outside the checkout, including credentials (T-50).
- **Assumptions**: None
- **Out of Scope**: Secrets isolation during content processing (REQ-PIPELINE-090); Hugo version pinning (REQ-PIPELINE-030).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build (content-processing stage)
- **Security Traceability**: SEC-INPUT-7

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build
- **Interfaces / Operations**: Check run on the checkout before any processing
- **Actors**: Editors (authors of untrusted content); build identity (SEC-CICD-1)
- **Preconditions**: REQ-CONTENT-030
- **Data Classification**: Multiple
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Confidentiality
- **Control Layers**: Input Validation
- **Threat References**: STRIDE Information Disclosure: SECURITY.md Threat Model v3 T-50; CWE-59
- **Abuse / Misuse Case**: An editor token commits a symbolic link named like an image that points to a credentials file on the runner
- **Trust Boundary**: TB-4 (Content Store → Site Build)
- **Untrusted Inputs or Assertions**: Every file and field in the Content Store checkout, including direct writes that bypassed the Editing Interface (T-49)
- **Authoritative Enforcement Point**: Site Build
- **Independent Verification**: Rechecks in the Site Build, independent of the checks in the editor's browser (DR-3)
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP Input Validation Cheat Sheet (`REF-INPUT`)
- **Mapping Basis**: Other: `REF-INPUT` as cited in SECURITY.md for this rule.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given a checkout with only regular files and directories, when the check runs, then it passes.
2. **AC-02 — Boundary or failure behavior**: Given a symbolic link `works/x/images/a.jpg` pointing outside the checkout, when the Site Build runs, then it fails naming the path, and the output contains nothing from the link target.
3. **AC-03 — Boundary or failure behavior**: Given a symbolic link pointing to another file inside the checkout, when the check runs, then it also fails; every symbolic link is rejected.
4. **AC-04 — Prohibited behavior**: Given a checkout with a FIFO, device file or Git submodule entry, when the check runs, then the build fails before Hugo starts and nothing is published.

## Failure Behavior

- **On Invalid Input**: The Site Build fails, names the content item and field, and publishes nothing; the previous site stays online (SEC-INPUT-3)
- **On Authentication Failure**: N/A: the Site Build has no sign-in; it runs as the build identity
- **On Authorization Failure**: N/A: no authorization decision is made in this step
- **On Security-Decision Failure**: Deny by default: if the check cannot complete, the build fails and publishes nothing
- **On External Dependency Failure**: N/A
- **On System Error**: The Site Build fails and publishes nothing; the previous site stays online (SEC-INPUT-3)
- **Logging / Audit**: The build log names the content item identifier and field; it contains no content values beyond identifiers and no secrets (SEC-SECRET-1, SEC-CICD-9)
- **Alerting**: A failed build emails Ives (REQ-PIPELINE-110); Nina sees it in the Editing Interface (REQ-EDIT-090)

## Test Strategy

- **Unit Tests**: Directory walk classifying entry types without following links
- **Integration Tests**: Build of a test content repository containing a committed symbolic link
- **Security Tests**: Symbolic link to a canary file outside the checkout: the canary value never appears in output or logs
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:CONTENT-070/AC-01`; AC-02 → `test:CONTENT-070/AC-02`; AC-03 → `test:CONTENT-070/AC-03`; AC-04 → `test:CONTENT-070/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model, including hostile fixtures

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-030 (https://github.com/dalini/nina-website/issues/22)
- **Downstream Requirements**: None
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: A failure of the check blocks publishing until the content is fixed; the previous site stays online

## Implementation Notes

- **Constraints**: Hugo, pinned at a version without known symlink-confinement advisories (`REF-HUGO-ADV`, REQ-PIPELINE-030); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Checks that run only in the editor's browser (DR-3); logging content values or secrets
- **Implementation Guidance**: Walk the tree without following links (lstat) before Hugo or any image tool reads it.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Trust-boundary control on the build machine; link handling in checkouts has edge cases (submodules, links to links) that need care.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
