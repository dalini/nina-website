## Metadata

- **ID**: REQ-IMAGES-040
- **Title**: Reject images larger than 40 megapixels before decoding them fully
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Reliability
- **Source / Parent**: SECURITY.md SEC-INPUT-5, `REF-INPUT`; REQUIREMENTS.md FR-8.5; parent {{ISSUE_URL:REQ-IMAGES-000}}

## Requirement

- **Statement**: Image processing in the Site Build MUST read an image's declared pixel dimensions and reject it before full decoding when it exceeds 40 megapixels: a Content Store image fails the build (SEC-INPUT-3); an Instagram image is left out with its post (REQ-INSTAGRAM-030).
- **Rationale**: A small file can declare huge dimensions and exhaust memory or time, blocking every publish (T-8, T-38).
- **Assumptions**: None
- **Out of Scope**: Editor-side upload checks (REQ-EDIT-050).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build image processing
- **Security Traceability**: SEC-INPUT-5

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build
- **Interfaces / Operations**: A shared pixel-limit check used by the image processing stage and by REQ-INSTAGRAM-030
- **Actors**: Editors (authors of untrusted content); build identity (SEC-CICD-1)
- **Preconditions**: REQ-IMAGES-010, REQ-CONTENT-030
- **Data Classification**: Multiple
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Availability
- **Control Layers**: Input Validation
- **Threat References**: STRIDE Denial of Service: SECURITY.md Threat Model v3 T-8, T-38; CWE-409, CWE-400
- **Abuse / Misuse Case**: A 200 KB PNG declaring 50000 × 50000 px is committed to make every build run out of memory
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

1. **AC-01 — Expected behavior**: Given a 7000 × 5000 px image (35 MP), when the build runs, then it is processed.
2. **AC-02 — Boundary or failure behavior**: Given a small file declaring 9000 × 5000 px (45 MP) in the Content Store, when the build runs, then it fails naming the item and field within normal build time and memory, and publishes nothing.
3. **AC-03 — Boundary or failure behavior**: Given the same file as fetched Instagram media, when the check is called by the feed step, then it reports a rejection and the build continues.
4. **AC-04 — Prohibited behavior**: Given any image over the limit, when it is checked, then it is never passed to the full decoder.

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

- **Unit Tests**: Header-only dimension reader for JPEG, PNG, WebP and GIF; limit at exactly 40 MP
- **Integration Tests**: Build with a decompression-bomb fixture under a memory limit
- **Security Tests**: Decompression-bomb corpus; malformed headers fail closed
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:IMAGES-040/AC-01`; AC-02 → `test:IMAGES-040/AC-02`; AC-03 → `test:IMAGES-040/AC-03`; AC-04 → `test:IMAGES-040/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model, including hostile fixtures

## Dependencies

- **Upstream Requirements**: REQ-IMAGES-010 ({{ISSUE_URL:REQ-IMAGES-010}}), REQ-CONTENT-030 ({{ISSUE_URL:REQ-CONTENT-030}})
- **Downstream Requirements**: REQ-INSTAGRAM-030 ({{ISSUE_URL:REQ-INSTAGRAM-030}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: A failure of the check blocks publishing until the content is fixed; the previous site stays online

## Implementation Notes

- **Constraints**: Hugo, pinned at a version without known symlink-confinement advisories (`REF-HUGO-ADV`, REQ-PIPELINE-030); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Checks that run only in the editor's browser (DR-3); logging content values or secrets
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Denial-of-service guard that must run before the generator's own decoder and handle malformed headers safely.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
