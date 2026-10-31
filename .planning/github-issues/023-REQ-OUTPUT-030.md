## Metadata

- **ID**: REQ-OUTPUT-030
- **Title**: Allow only https: and mailto: links in content and open external links with noopener
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-OUT-2, `REF-XSS`, `REF-INPUT`; REQUIREMENTS.md FR-4.3, FR-4.6, FR-7.1, FR-7.2, FR-8.1; parent {{ISSUE_URL:REQ-OUTPUT-000}}

## Requirement

- **Statement**: External links from content (exhibition and news links, the gallery link, the Instagram link, links in rich text) MUST use only the `https:` or `mailto:` scheme, and every external link MUST carry `rel="noopener"`; content with any other scheme fails the build (REQ-CONTENT-030).
- **Rationale**: Other schemes (`javascript:`, `data:`, `http:`) allow script or downgrade; `noopener` keeps the target page away from the site (T-38).
- **Assumptions**: None
- **Out of Scope**: Instagram post links fetched at build time, which are left out with their post instead (REQ-INSTAGRAM-030).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build; content model
- **Security Traceability**: SEC-OUT-2

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build
- **Interfaces / Operations**: Link fields of Exhibition entry, News item, Contact page; links in rich text
- **Actors**: Editors (authors of untrusted content); build identity (SEC-CICD-1)
- **Preconditions**: REQ-CONTENT-010, REQ-CONTENT-020, REQ-CONTENT-050, REQ-CONTENT-030
- **Data Classification**: Multiple
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Input Validation, Output Encoding
- **Threat References**: STRIDE Tampering: SECURITY.md Threat Model v2 T-38; CWE-79
- **Abuse / Misuse Case**: An external link of `javascript:fetch(...)` or `data:text/html,...` is entered on an Exhibition entry
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
- **Other**: OWASP XSS Prevention Cheat Sheet (`REF-XSS`); OWASP Input Validation Cheat Sheet (`REF-INPUT`)
- **Mapping Basis**: Other: `REF-XSS` and `REF-INPUT` as cited in SECURITY.md for this rule.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given an Exhibition link `https://example.org/show` and the Contact email as `mailto:`, when the build runs, then both render, the https link with `rel="noopener"`.
2. **AC-02 — Boundary or failure behavior**: Given links with `javascript:`, `JaVaScRiPt:`, `data:`, `http:` and `ftp:` schemes in link fields and in rich text, when the build runs, then it fails naming each item and field.
3. **AC-03 — Prohibited behavior**: Given the output, when every `a` element is checked, then none has a scheme other than `https:`, `mailto:` or a site-relative path, and every external one has `noopener`.

## Failure Behavior

- **On Invalid Input**: The Site Build fails, names the content item and field, and publishes nothing; the previous site stays online (SEC-INPUT-3)
- **On Authentication Failure**: N/A: the Site Build has no sign-in; it runs as the build identity
- **On Authorization Failure**: N/A: no authorization decision is made in this step
- **On Security-Decision Failure**: Deny by default: if the check cannot complete, the build fails and publishes nothing
- **On External Dependency Failure**: N/A
- **On System Error**: The Site Build fails and publishes nothing; the previous site stays online (SEC-INPUT-3)
- **Logging / Audit**: The build log names the content item identifier and field; it contains no content values beyond identifiers and no secrets (SEC-SECRET-1, SEC-CICD-9)
- **Alerting**: A failed build emails Ives (REQ-PIPELINE-110)

## Test Strategy

- **Unit Tests**: Scheme allow-list with case, whitespace and encoding variants
- **Integration Tests**: Build with link fixtures in every link field
- **Security Tests**: Bypass corpus for scheme parsing (leading spaces, tabs, entity-encoded colons)
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:OUTPUT-030/AC-01`; AC-02 → `test:OUTPUT-030/AC-02`; AC-03 → `test:OUTPUT-030/AC-03` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model, including hostile fixtures

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-010 ({{ISSUE_URL:REQ-CONTENT-010}}), REQ-CONTENT-020 ({{ISSUE_URL:REQ-CONTENT-020}}), REQ-CONTENT-050 ({{ISSUE_URL:REQ-CONTENT-050}}), REQ-CONTENT-030 ({{ISSUE_URL:REQ-CONTENT-030}})
- **Downstream Requirements**: REQ-EXHIBITIONS-010 ({{ISSUE_URL:REQ-EXHIBITIONS-010}}), REQ-NEWS-010 ({{ISSUE_URL:REQ-NEWS-010}}), REQ-CONTACT-010 ({{ISSUE_URL:REQ-CONTACT-010}}), REQ-INSTAGRAM-010 ({{ISSUE_URL:REQ-INSTAGRAM-010}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: A failure of the check blocks publishing until the content is fixed; the previous site stays online

## Implementation Notes

- **Constraints**: Hugo, pinned at a version without known symlink-confinement advisories (`REF-HUGO-ADV`, REQ-PIPELINE-030); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Checks that run only in the editor's browser (DR-3); logging content values or secrets
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; NOT VERIFIED, see ISSUE_PLAN.md Planning Rules), because: A small allow-list with a known bypass corpus, fully specified by SEC-OUT-2.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
