## Metadata

- **ID**: REQ-IMAGES-010
- **Title**: Publish images only as responsive sizes of at most 2400 px on the long side, never as originals
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Performance
- **Source / Parent**: REQUIREMENTS.md FR-3.10, NFR-1; SECURITY.md T-25; ARCHITECTURE.md Site Build (responsive image sizes); parent {{ISSUE_URL:REQ-IMAGES-000}}

## Requirement

- **Statement**: The Site Build MUST publish every image from the Content Store only as generated sizes suited to the visitor's screen, each at most 2400 px on its long side, and MUST NOT publish any uploaded original file.
- **Rationale**: ~4 MB originals are too heavy for mobile (NFR-1) and must not be published (FR-3.10, T-25).
- **Assumptions**: None
- **Out of Scope**: Metadata removal (REQ-IMAGES-020); pixel and type checks (REQ-IMAGES-040, REQ-IMAGES-050); performance budget (REQ-PERF-010).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build (responsive image sizes from the originals, 2400 px maximum)
- **Security Traceability**: SEC-PRIV-4 (no original files with metadata reach the output; stripping is REQ-IMAGES-020)

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build
- **Interfaces / Operations**: Image processing for work images, News images, Text-body images and the home image
- **Actors**: Editors (authors of untrusted content); build identity (SEC-CICD-1)
- **Preconditions**: REQ-CONTENT-010
- **Data Classification**: Multiple
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Confidentiality, Availability
- **Control Layers**: Data Protection
- **Threat References**: STRIDE Information Disclosure: SECURITY.md Threat Model v3 T-25; CWE-200
- **Abuse / Misuse Case**: A visitor downloads the full-resolution original by guessing its path
- **Trust Boundary**: TB-4 (Content Store → Site Build)
- **Untrusted Inputs or Assertions**: Uploaded image originals
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

1. **AC-01 — Expected behavior**: Given a 6000 × 4000 px original, when the build runs, then the output holds several sizes, the largest 2400 × 1600 px, and pages reference them with `srcset` and `sizes`.
2. **AC-02 — Boundary or failure behavior**: Given a 1200 × 800 px original, when the build runs, then no size larger than 1200 px wide is produced.
3. **AC-03 — Prohibited behavior**: Given the output, when every image file is compared with the originals, then no file is byte-identical to an original and none exceeds 2400 px on its long side.
4. **AC-04 — Additional criterion**: Given a 375 px wide phone screen, when a work page loads, then the browser selects a size far smaller than the largest one.

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

- **Unit Tests**: Size ladder selection; long-side cap
- **Integration Tests**: Build with large fixture originals; output scan of dimensions and hashes
- **Security Tests**: Output hash comparison against originals; guessed original paths return 404 on the test host
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:IMAGES-010/AC-01`; AC-02 → `test:IMAGES-010/AC-02`; AC-03 → `test:IMAGES-010/AC-03`; AC-04 → `test:IMAGES-010/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model, including hostile fixtures

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-010 ({{ISSUE_URL:REQ-CONTENT-010}})
- **Downstream Requirements**: REQ-HOME-010 ({{ISSUE_URL:REQ-HOME-010}}), REQ-WORKS-010 ({{ISSUE_URL:REQ-WORKS-010}}), REQ-WORKS-030 ({{ISSUE_URL:REQ-WORKS-030}}), REQ-WORKS-050 ({{ISSUE_URL:REQ-WORKS-050}}), REQ-IMAGES-020 ({{ISSUE_URL:REQ-IMAGES-020}}), REQ-IMAGES-040 ({{ISSUE_URL:REQ-IMAGES-040}}), REQ-EXHIBITIONS-030 ({{ISSUE_URL:REQ-EXHIBITIONS-030}}), REQ-NEWS-010 ({{ISSUE_URL:REQ-NEWS-010}}), REQ-TEXTS-010 ({{ISSUE_URL:REQ-TEXTS-010}}), REQ-PERF-010 ({{ISSUE_URL:REQ-PERF-010}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: A failure of the check blocks publishing until the content is fixed; the previous site stays online

## Implementation Notes

- **Constraints**: Hugo, pinned at a version without known symlink-confinement advisories (`REF-HUGO-ADV`, REQ-PIPELINE-030); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Checks that run only in the editor's browser (DR-3); logging content values or secrets
- **Implementation Guidance**: Hugo publishes page resources on its own when they are referenced; ensure only processed images are written.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Generator resource handling can leak originals silently (T-25); getting sizes, caps and output hygiene right needs care.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
