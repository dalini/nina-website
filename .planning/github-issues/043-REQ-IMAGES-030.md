## Metadata

- **ID**: REQ-IMAGES-030
- **Title**: Remove embedded metadata from published PDFs automatically, except the copyright notice and the creator
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Privacy
- **Source / Parent**: SECURITY.md SEC-PRIV-4 (PDFs cleaned automatically, step 10 again), T-25; REQUIREMENTS.md FR-6.1; ARCHITECTURE.md Site Build (metadata stripping); parent https://github.com/dalini/nina-website/issues/7

## Requirement

- **Statement**: The Site Build MUST remove embedded metadata (author, software, location, including metadata of images embedded in the PDF) from every PDF it publishes, automatically and without failing the publish because metadata was present, and MUST keep only the copyright notice and the creator (photographer or author).
- **Rationale**: PDFs Nina uploads can carry her location, software and author data (T-25); step 10 again chose automatic cleaning so that a publish never fails for PDF metadata.
- **Assumptions**: None
- **Out of Scope**: Image metadata (REQ-IMAGES-020); the PDF type and size recheck (REQ-IMAGES-050); listing Downloads (REQ-DOWNLOADS-010).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build (metadata stripping from images and PDFs)
- **Security Traceability**: SEC-PRIV-4

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build
- **Interfaces / Operations**: PDF processing step of the Site Build, for every published Download
- **Actors**: Editors (authors of untrusted content); build identity (SEC-CICD-1)
- **Preconditions**: REQ-CONTENT-020, REQ-IMAGES-050
- **Data Classification**: Multiple
- **Personal or Regulated Data**: Personal Data (author names, locations in PDF and embedded-image metadata)
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Privacy
- **Control Layers**: Data Protection, Sanitization
- **Threat References**: STRIDE Information Disclosure: SECURITY.md Threat Model v3 T-25; CWE-212
- **Abuse / Misuse Case**: N/A: the risk is accidental disclosure of Nina's location or author data
- **Trust Boundary**: TB-4 (Content Store → Site Build)
- **Untrusted Inputs or Assertions**: PDF files from the Content Store
- **Authoritative Enforcement Point**: Site Build
- **Independent Verification**: Rechecks in the Site Build, independent of the checks in the editor's browser (DR-3)
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP Top 10 Proactive Controls 2024 (`REF-PC-2024`)
- **Mapping Basis**: Other: `REF-PC-2024` as cited in SECURITY.md SEC-PRIV-4.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given a published PDF whose document info and XMP name an author, a producer program and a location, and which embeds a JPEG with GPS data, when the build runs, then the published PDF keeps its pages and its copyright notice and creator, and holds none of the other metadata.
2. **AC-02 — Boundary or failure behavior**: Given a PDF without any metadata, when the build runs, then it is published unchanged in content and the publish succeeds.
3. **AC-03 — Boundary or failure behavior**: Given a PDF whose metadata cannot be fully removed by the tool, when the build runs, then the build fails and names the Download, rather than publishing the file with metadata.
4. **AC-04 — Prohibited behavior**: Given any build, when the whole output is scanned, then no PDF or image file in it contains GPS or device metadata (SEC-PRIV-4), and no publish failed only because a PDF had metadata.

## Failure Behavior

- **On Invalid Input**: A PDF the cleaning tool cannot parse fails the publish, naming the Download (SEC-INPUT-3); a PDF with metadata never does
- **On Authentication Failure**: N/A: the Site Build has no sign-in; it runs as the build identity
- **On Authorization Failure**: N/A: no authorization decision is made in this step
- **On Security-Decision Failure**: Deny by default: if the check cannot complete, the build fails and publishes nothing
- **On External Dependency Failure**: N/A
- **On System Error**: The Site Build fails and publishes nothing; the previous site stays online (SEC-INPUT-3)
- **Logging / Audit**: The build log names the content item identifier and field; it contains no content values beyond identifiers and no secrets (SEC-SECRET-1, SEC-CICD-9)
- **Alerting**: A failed build emails Ives (REQ-PIPELINE-110); Nina sees it in the Editing Interface (REQ-EDIT-090)

## Test Strategy

- **Unit Tests**: Keep-list of copyright and creator fields; removal of document info, XMP and embedded-image EXIF
- **Integration Tests**: Build of a test content repository with PDF fixtures carrying each kind of metadata
- **Security Tests**: Whole-output metadata scan for GPS and device fields across images and PDFs
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:IMAGES-030/AC-01`; AC-02 → `test:IMAGES-030/AC-02`; AC-03 → `test:IMAGES-030/AC-03`; AC-04 → `test:IMAGES-030/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model, including hostile fixtures

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-020 (https://github.com/dalini/nina-website/issues/19), REQ-IMAGES-050 (https://github.com/dalini/nina-website/issues/44)
- **Downstream Requirements**: REQ-DOWNLOADS-010 (https://github.com/dalini/nina-website/issues/56)
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: A failure of the check blocks publishing until the content is fixed; the previous site stays online

## Implementation Notes

- **Constraints**: Hugo, pinned at a version without known advisories (`REF-HUGO-ADV`, `REF-HUGO-SEC`, REQ-PIPELINE-030); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Checks that run only in the editor's browser (DR-3); logging content values or secrets
- **Implementation Guidance**: Pin the PDF tool like other build tools (SEC-CICD-2) and run it in the content-processing job without secrets (SEC-CICD-6).
- **AI Development Guidance**: Follow CLAUDE.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: A privacy control over a complex file format (document info, XMP, embedded images) with a keep-list, where a publish must never fail because of metadata.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
