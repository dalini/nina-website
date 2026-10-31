## Metadata

- **ID**: REQ-LEGAL-010
- **Title**: Publish the Impressum and Datenschutzerklärung pages and link them from every page
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: Critical
- **Requirement Type**: Compliance
- **Source / Parent**: REQUIREMENTS.md FR-9.1, Business objects (Page: Impressum, Datenschutzerklärung); SECURITY.md Required Security Inputs (GDPR, Impressum); parent {{ISSUE_URL:REQ-EPIC-001}}

## Requirement

- **Statement**: The Site Build MUST generate the Impressum and the Datenschutzerklärung from their Page content in German and English, and every public page MUST link to both.
- **Rationale**: The site is operated from Germany; an Impressum and a Datenschutzerklärung are required (FR-9.1).
- **Assumptions**: None
- **Out of Scope**: The legal wording itself (Nina's content, drafted with a German legal-text generator); the build failure on a missing legal Page (FR-10.6); Datenschutzerklärung statements about access logs and log statistics (SECURITY.md SEC-PRIV-5, SEC-PRIV-6), which are content.
- **Design Traceability**: DESIGN.md Components (Links); Typography (68-character text width); DESIGN.md Accessibility (WCAG 2.2 AA, visible focus, keyboard use, `lang`); `style-guide.html` as the rendered reference
- **Architecture Traceability**: Content Store (Page), Site Build; DR-2
- **Security Traceability**: SEC-OUT-1 (via REQ-OUTPUT-010)

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Impressum page, Datenschutzerklärung page, shared page layout
- **Actors**: Visitor (anonymous)
- **Preconditions**: REQ-CONTENT-020 (Page model), REQ-NAV-020 (bilingual pages)
- **Data Classification**: Public
- **Personal or Regulated Data**: Personal Data (Nina's name and contact data in the Impressum, published on purpose)
- **Jurisdiction / Regulatory Scope**: Germany / EU: GDPR; Impressum obligation; wording drafted with a German legal-text generator and approved by Nina (SECURITY.md)

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Output Encoding
- **Threat References**: N/A beyond rendering risks handled in REQ-OUTPUT-010 and REQ-OUTPUT-030
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

1. **AC-01 — Expected behavior**: Given Impressum and Datenschutzerklärung Page content in both languages, when the Site Build runs, then both pages exist in German and English and show that content.
2. **AC-02 — Expected behavior**: Given any generated page in either language, when it is loaded, then it contains a link to the Impressum and a link to the Datenschutzerklärung in the same language.
3. **AC-03 — Boundary or failure behavior**: Given Datenschutzerklärung Page content containing a `<script>` element, when the Site Build runs, then the generated page contains no executable script (SEC-OUT-1, via REQ-OUTPUT-010).
4. **AC-04 — Prohibited behavior**: Given the generated site, when the legal links are followed, then none leads to a missing page or to another origin.

## Failure Behavior

- **On Invalid Input**: Content violating the model fails the build (REQ-CONTENT-030)
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication (SECURITY.md SEC-AUTHN-1 to -3) is decomposed in the step 9 re-run
- **On Authorization Failure**: N/A: all public pages are read-only and need no account (NFR-2)
- **On Security-Decision Failure**: N/A: this page makes no security decision; encoding and link rules are enforced by REQ-OUTPUT-010 and REQ-OUTPUT-030
- **On External Dependency Failure**: N/A
- **On System Error**: The Site Build fails and publishes nothing (SEC-INPUT-3); no partial page set is deployed
- **Logging / Audit**: Build log names the content item and field that failed; no secrets in the log (SEC-SECRET-1)
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: Legal-link presence in the shared layout
- **Integration Tests**: Full build: every output page links to both legal pages in its language
- **Security Tests**: Covered by REQ-OUTPUT-010 fixtures run against this page type; REQ-PRIV-010 network test includes this page type
- **Compliance Tests / Evidence**: Manual evidence before release: legal wording approved by Nina; screenshot of both links on each page type
- **Acceptance-Criteria Traceability**: AC-01 → `test:LEGAL-010/AC-01`; AC-02 → `test:LEGAL-010/AC-02`; AC-03 → `test:LEGAL-010/AC-03`; AC-04 → `test:LEGAL-010/AC-04`
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build run locally against fixture content in the documented content model; Static site generator: Hugo (ARCHITECTURE.md Site Build); the last two major versions of Chrome, Edge, Firefox, Safari (macOS, iOS) and Samsung Internet (NFR-4); screen reader check

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-020 ({{ISSUE_URL:REQ-CONTENT-020}}), REQ-NAV-020 ({{ISSUE_URL:REQ-NAV-020}}), REQ-OUTPUT-010 ({{ISSUE_URL:REQ-OUTPUT-010}})
- **Downstream Requirements**: None
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A

## Implementation Notes

- **Constraints**: Static site generator: Hugo (ARCHITECTURE.md Site Build); DEP-1 to DEP-8 apply to every dependency added; legal text is Page content, not template text (DR-2)
- **Prohibited Approaches**: Hard-coding legal text in templates; linking to an externally hosted legal page
- **Implementation Guidance**: The legal links can sit in the shared footer; placement inside the page is a design detail DESIGN.md leaves open.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`), because: Two Page renders plus footer links; wording is content.
- **Required Human Review**: Legal/privacy review of the wording (outside this issue); product review by Nina
- **Open Decisions**: None (step 10: Hugo; browsers NFR-4; Nina approves the legal wording).
