## Metadata

- **ID**: REQ-CONTENT-040
- **Title**: Exclude unpublished content from the static output
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Repository maintainer (dalini); the maintainer role is still open in SECURITY.md SQ-10
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: Critical
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-INPUT-4; REQUIREMENTS.md FR-3.1, FR-5.1, FR-6.1, FR-10.1 (unpublish), FR-11.2; parent {{ISSUE_URL:REQ-CONTENT-000}}

## Requirement

- **Statement**: The Site Build MUST NOT write any page, listing entry, image or file of an unpublished content item to the static output.
- **Rationale**: Unpublished content must stay private (SEC-INPUT-4); lists show only published items (FR-3.1, FR-5.1); legacy works not selected must not be published (FR-11.2).
- **Assumptions**: None
- **Out of Scope**: Preview builds of unpublished content (REQ-PIPELINE-060); read access to the Content Store (SEC-AUTHZ-4, blocked by PQ-4).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build (generates only published content); DR-1
- **Security Traceability**: SEC-INPUT-4

## Scope

- **Applies To**: Background Processing
- **Components**: Content Store, Site Build
- **Interfaces / Operations**: Site Build output for every page type and every media file
- **Actors**: Editor (indirectly, through stored content); Site Build
- **Preconditions**: REQ-CONTENT-010 and REQ-CONTENT-020 define the publish state
- **Data Classification**: Internal (unpublished content)
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Confidentiality
- **Control Layers**: Data Protection, Business-Rule Validation
- **Threat References**: STRIDE Information Disclosure: SECURITY.md Threat Model v1 T-21, T-25, T-29 (related)
- **Abuse / Misuse Case**: An unpublished work's images or page reach the public site through a listing, a direct file copy or a sitemap.
- **Trust Boundary**: TB-4 (Content Store → Site Build, DF-5)
- **Untrusted Inputs or Assertions**: Every content file read from the Content Store, including legacy imports (DF-3)
- **Authoritative Enforcement Point**: Site Build (recheck, DR-3); the Editing Interface check is blocked scope (PQ-1)
- **Independent Verification**: The Site Build validates content independently of the Editing Interface that wrote it (DR-3)
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: TO BE DECIDED: SECURITY.md cites ASVS 5.0.0 but uses no ASVS identifiers until they are verified; assurance level open in SECURITY.md SQ-9
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: TO BE DECIDED: not assessed
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: N/A
- **Mapping Basis**: Confidentiality of unpublished content; no standard control mapped.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given one published and one unpublished Work, Text and Download, when the Site Build runs, then the output contains the published items in both languages.
2. **AC-02 — Boundary or failure behavior**: Given an unpublished Work with images, when the Site Build runs, then the output contains no page, listing entry, image file or link for it in either language.
3. **AC-03 — Prohibited behavior**: Given unpublished items, when the whole output directory is searched for their identifiers, titles and media file names, then none is found.

## Failure Behavior

- **On Invalid Input**: A publish state missing or not recognized is a content-model violation (REQ-CONTENT-030)
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication is blocked scope (ISSUE_PLAN.md PQ-2)
- **On Authorization Failure**: N/A: the Site Build only reads content
- **On Security-Decision Failure**: Content whose publish state cannot be determined is treated as unpublished
- **On External Dependency Failure**: N/A
- **On System Error**: The Site Build stops with a failure and writes no deployable output (SEC-INPUT-3)
- **Logging / Audit**: Build log: content type, item identifier and field for each violation; no content values beyond the identifier; no secrets
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: Publish-state filter for every content type
- **Integration Tests**: Full build with mixed fixtures; scan of the output tree
- **Security Tests**: Output scan for unpublished identifiers, titles and media file names in HTML, sitemaps, feeds and copied assets
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:CONTENT-040/AC-01`; AC-02 → `test:CONTENT-040/AC-02`; AC-03 → `test:CONTENT-040/AC-03`
- **Coverage Target**: Project coverage target TO BE DECIDED (build and test commands are TO BE DECIDED in CLAUDE.md). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build run locally against fixture content in the documented content model; Static site generator: TO BE DECIDED (PQ-16, ARCHITECTURE.md AQ-1)

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-010 ({{ISSUE_URL:REQ-CONTENT-010}}), REQ-CONTENT-020 ({{ISSUE_URL:REQ-CONTENT-020}})
- **Downstream Requirements**: REQ-DOWNLOADS-010 ({{ISSUE_URL:REQ-DOWNLOADS-010}}), REQ-TEXTS-010 ({{ISSUE_URL:REQ-TEXTS-010}}), REQ-WORKS-010 ({{ISSUE_URL:REQ-WORKS-010}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A

## Implementation Notes

- **Constraints**: Static site generator: TO BE DECIDED (PQ-16, ARCHITECTURE.md AQ-1); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Hiding unpublished items only in templates or with CSS while files are still copied
- **Implementation Guidance**: Filter at the content-loading step so templates never receive unpublished items.
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`), because: Confidentiality control with a subtle failure mode: generators copy page resources and sitemaps on their own (SECURITY.md T-25, T-29).
- **Required Human Review**: Security review
- **Open Decisions**: Non-blocking: PQ-16.
