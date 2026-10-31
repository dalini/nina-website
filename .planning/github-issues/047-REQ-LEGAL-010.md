## Metadata

- **ID**: REQ-LEGAL-010
- **Title**: Publish the Impressum and Datenschutzerklärung pages and link them from every page
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Compliance
- **Source / Parent**: REQUIREMENTS.md FR-9.1; SECURITY.md Applicable privacy or regulatory obligations, SEC-PRIV-5, SEC-PRIV-6; parent {{ISSUE_URL:REQ-EPIC-001}}

## Requirement

- **Statement**: The Site Build MUST publish the Impressum and the Datenschutzerklärung from their Page content in both languages, and every page MUST link to both.
- **Rationale**: A site operated from Germany needs both pages (FR-9.1).
- **Assumptions**: None
- **Out of Scope**: The legal wording, drafted with a German legal-text generator and approved by Nina; it must describe the access logs and log statistics (SECURITY.md).
- **Design Traceability**: DESIGN.md Components (Links), Layout and Spacing (text width); DESIGN.md Accessibility (WCAG 2.2 AA, visible focus, keyboard use, `lang`); `style-guide.html` as the rendered reference
- **Architecture Traceability**: Site Build; Public Site
- **Security Traceability**: SEC-OUT-1 (Page content is rich text, via REQ-OUTPUT-010)

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Footer of every page; Impressum and Datenschutzerklärung pages
- **Actors**: Visitor (anonymous)
- **Preconditions**: REQ-CONTENT-020, REQ-CONTENT-030, REQ-NAV-020, REQ-OUTPUT-010
- **Data Classification**: Public
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: Germany / EU: GDPR; Impressum obligation

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Output Encoding
- **Threat References**: N/A: rendering risks are handled by REQ-OUTPUT-010 and REQ-OUTPUT-030
- **Abuse / Misuse Case**: N/A
- **Trust Boundary**: TB-1 (static pages served to visitors)
- **Untrusted Inputs or Assertions**: Editor content rendered on the page (encoded by REQ-OUTPUT-010)
- **Authoritative Enforcement Point**: Site Build
- **Independent Verification**: N/A
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: GDPR; Impressum obligation. No statute or article mapping is kept (SECURITY.md Applicable privacy or regulatory obligations)
- **Other**: W3C WCAG 2.2, Level AA (DESIGN.md Accessibility target)
- **Mapping Basis**: Other: WCAG 2.2 Level AA as targeted by DESIGN.md.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given both Pages exist, when the site is built, then every page in both languages has footer links to both, and each opens the page in the same language.
2. **AC-02 — Boundary or failure behavior**: Given the Datenschutzerklärung Page is missing, when the Site Build runs, then it fails under REQ-CONTENT-030 and the previous site stays online.
3. **AC-03 — Prohibited behavior**: Given any page, when its footer is inspected, then neither legal link is hidden, requires a sign-in, or leads off the site.

## Failure Behavior

- **On Invalid Input**: Content that violates the content model fails the build (REQ-CONTENT-030); nothing is published
- **On Authentication Failure**: N/A: public pages need no sign-in (NFR-2)
- **On Authorization Failure**: N/A: all public pages are read-only and need no account (NFR-2)
- **On Security-Decision Failure**: N/A: this page makes no security decision; encoding and link rules are enforced by REQ-OUTPUT-010 and REQ-OUTPUT-030
- **On External Dependency Failure**: N/A
- **On System Error**: The Site Build fails and publishes nothing; the previous site stays online (SEC-INPUT-3)
- **Logging / Audit**: The build log names the failing content item and field; it contains no secrets (SEC-SECRET-1)
- **Alerting**: A failed build emails Ives (REQ-PIPELINE-110)

## Test Strategy

- **Unit Tests**: N/A: template; covered by integration tests
- **Integration Tests**: Crawl: every page links to both legal pages
- **Security Tests**: REQ-OUTPUT-010 hostile fixtures run against this page type; the REQ-PRIV-010 network test covers this page type
- **Compliance Tests / Evidence**: Evidence: crawl report showing both links on every page; Nina's approval of the wording, including the description of access logs (SEC-PRIV-6) and log statistics (SEC-PRIV-5)
- **Acceptance-Criteria Traceability**: AC-01 → `test:LEGAL-010/AC-01`; AC-02 → `test:LEGAL-010/AC-02`; AC-03 → `test:LEGAL-010/AC-03` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model; the last two major versions of Chrome, Edge, Firefox, Safari (macOS, iOS) and Samsung Internet (NFR-4); keyboard-only and screen reader check

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-020 ({{ISSUE_URL:REQ-CONTENT-020}}), REQ-CONTENT-030 ({{ISSUE_URL:REQ-CONTENT-030}}), REQ-NAV-020 ({{ISSUE_URL:REQ-NAV-020}}), REQ-OUTPUT-010 ({{ISSUE_URL:REQ-OUTPUT-010}})
- **Downstream Requirements**: REQ-PERF-010 ({{ISSUE_URL:REQ-PERF-010}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A: no runtime dependency; a failed build leaves the previous site online

## Implementation Notes

- **Constraints**: Hugo (ARCHITECTURE.md Site Build); no client framework, and any first-party JavaScript follows SEC-OUT-5 (values inserted as text only); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Template text that editors are expected to change (DR-2); content rules evaluated in the visitor's browser (DR-3)
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; NOT VERIFIED, see ISSUE_PLAN.md Planning Rules), because: Two Page renders and footer links; the wording is content.
- **Required Human Review**: Product and legal review by Nina (wording approval); design review against DESIGN.md
- **Open Decisions**: None
