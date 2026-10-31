## Metadata

- **ID**: REQ-IMAGES-020
- **Title**: Remove embedded metadata from published images except the copyright notice and the creator
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Privacy
- **Source / Parent**: SECURITY.md SEC-PRIV-4, `REF-PC-2024`; REQUIREMENTS.md FR-3.10; parent https://github.com/dalini/nina-website/issues/7

## Requirement

- **Statement**: The Site Build MUST remove EXIF and all other embedded metadata, including location, device, software and dates, from every published image, keeping only the copyright notice and the creator (photographer).
- **Rationale**: Photos can reveal where Nina lives or works (T-25); copyright and creator stay with the image.
- **Assumptions**: None
- **Out of Scope**: PDF metadata (REQ-IMAGES-030); Instagram media go through the same processing (REQ-INSTAGRAM-030).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build image processing
- **Security Traceability**: SEC-PRIV-4

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build
- **Interfaces / Operations**: Image processing stage
- **Actors**: Editors (authors of untrusted content); build identity (SEC-CICD-1)
- **Preconditions**: REQ-IMAGES-010
- **Data Classification**: Multiple
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Privacy
- **Control Layers**: Data Protection
- **Threat References**: STRIDE Information Disclosure: SECURITY.md Threat Model v3 T-25; CWE-212
- **Abuse / Misuse Case**: A visitor reads GPS coordinates from an image on the site
- **Trust Boundary**: TB-4 (Content Store → Site Build)
- **Untrusted Inputs or Assertions**: Embedded metadata in uploaded images
- **Authoritative Enforcement Point**: Site Build
- **Independent Verification**: Rechecks in the Site Build, independent of the checks in the editor's browser (DR-3)
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: GDPR (Regulation (EU) 2016/679); no article mapping is kept (SECURITY.md)
- **Other**: OWASP Top 10 Proactive Controls 2024 (`REF-PC-2024`)
- **Mapping Basis**: Other: `REF-PC-2024` as cited in SECURITY.md; Regulatory: GDPR, article not assessed.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given an original with GPS, camera model, software, date, copyright `© Nina Laaf` and creator `Max Muster`, when the build runs, then every published size keeps the copyright and creator and none of the other fields.
2. **AC-02 — Boundary or failure behavior**: Given an original without any metadata, when the build runs, then the published sizes carry no metadata and the build succeeds.
3. **AC-03 — Prohibited behavior**: Given the whole build output, when it is scanned with a metadata reader, then no image contains GPS, device or software fields.

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

- **Unit Tests**: Metadata filter with keep-list (copyright, creator)
- **Integration Tests**: Build with fixture originals carrying EXIF, XMP and IPTC blocks
- **Security Tests**: Automated output scan for GPS and device fields across all images (SEC-PRIV-4 Verification)
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:IMAGES-020/AC-01`; AC-02 → `test:IMAGES-020/AC-02`; AC-03 → `test:IMAGES-020/AC-03` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model, including hostile fixtures

## Dependencies

- **Upstream Requirements**: REQ-IMAGES-010 (https://github.com/dalini/nina-website/issues/37)
- **Downstream Requirements**: REQ-INSTAGRAM-030 (https://github.com/dalini/nina-website/issues/98)
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: A failure of the check blocks publishing until the content is fixed; the previous site stays online

## Implementation Notes

- **Constraints**: Hugo, pinned at a version without known symlink-confinement advisories (`REF-HUGO-ADV`, REQ-PIPELINE-030); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Checks that run only in the editor's browser (DR-3); logging content values or secrets
- **Implementation Guidance**: Re-encoding usually drops metadata; the keep-list must then be written back. Any metadata tool added falls under DEP-1 to DEP-8 and SEC-CICD-2.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Privacy control with format-specific metadata blocks (EXIF, XMP, IPTC) and a keep-list that is easy to get wrong.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
