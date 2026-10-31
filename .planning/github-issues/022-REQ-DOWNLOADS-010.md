## Metadata

- **ID**: REQ-DOWNLOADS-010
- **Title**: Offer published portfolio PDFs for download, each labeled with its title and as a PDF
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Repository maintainer (dalini); the maintainer role is still open in SECURITY.md SQ-10
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: Medium
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-6.1, FR-6.2; parent {{ISSUE_URL:REQ-EPIC-001}}

## Requirement

- **Statement**: The site MUST offer every published Download for download, and each download link MUST show the Download's title and that the file is a PDF before the visitor opens it.
- **Rationale**: Galleries and press request portfolios as PDFs (FR-6.1); visitors know what they open (FR-6.2).
- **Assumptions**: None
- **Out of Scope**: Which PDFs (OQ-4, PQ-22); on which page downloads appear (PQ-19); PDF metadata checks (SEC-PRIV-4, blocked by PQ-5, PQ-6).
- **Design Traceability**: DESIGN.md Components (Links); DESIGN.md Accessibility (WCAG 2.2 AA, visible focus, keyboard use, `lang`); `style-guide.html` as the rendered reference
- **Architecture Traceability**: Content Store (Download), Site Build
- **Security Traceability**: SEC-OUT-1 (via REQ-OUTPUT-010)

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Download links (DE, EN) on the page chosen under PQ-19
- **Actors**: Visitor (anonymous)
- **Preconditions**: REQ-CONTENT-020, REQ-CONTENT-040, REQ-NAV-020
- **Data Classification**: Public
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

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

- **OWASP ASVS 5.0.0**: TO BE DECIDED: SECURITY.md cites ASVS 5.0.0 but uses no ASVS identifiers until they are verified; assurance level open in SECURITY.md SQ-9
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: TO BE DECIDED: not assessed
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: W3C WCAG 2.2, Level AA (DESIGN.md Accessibility target)
- **Mapping Basis**: Other: WCAG 2.2 Level AA as targeted by DESIGN.md.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given two published Downloads, when the Site Build runs, then the site shows a link for each with its title in the page's language and a visible text indication that it is a PDF, and following a link delivers that PDF file.
2. **AC-02 — Boundary or failure behavior**: Given no published Download, when the Site Build runs, then the site renders without errors and shows no download link.
3. **AC-03 — Prohibited behavior**: Given an unpublished Download, when the site is generated, then its PDF file is not in the output (SEC-INPUT-4).

## Failure Behavior

- **On Invalid Input**: Content violating the model fails the build (REQ-CONTENT-030)
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication is blocked scope (ISSUE_PLAN.md PQ-2)
- **On Authorization Failure**: N/A: all public pages are read-only and need no account (NFR-2)
- **On Security-Decision Failure**: N/A: this page makes no security decision; encoding and link rules are enforced by REQ-OUTPUT-010 and REQ-OUTPUT-030
- **On External Dependency Failure**: N/A
- **On System Error**: The Site Build fails and publishes nothing (SEC-INPUT-3); no partial page set is deployed
- **Logging / Audit**: Build log names the content item and field that failed; no secrets in the log (SEC-SECRET-1)
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: Download link label (title plus PDF indication)
- **Integration Tests**: Full build: links resolve to the PDF files in the output
- **Security Tests**: Covered by REQ-OUTPUT-010 fixtures run against this page type; REQ-PRIV-010 network test includes this page type
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:DOWNLOADS-010/AC-01`; AC-02 → `test:DOWNLOADS-010/AC-02`; AC-03 → `test:DOWNLOADS-010/AC-03`
- **Coverage Target**: Project coverage target TO BE DECIDED (build and test commands are TO BE DECIDED in CLAUDE.md). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build run locally against fixture content in the documented content model; Static site generator: TO BE DECIDED (PQ-16, ARCHITECTURE.md AQ-1); current desktop and mobile browsers (list: PQ-21); screen reader check

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-020 ({{ISSUE_URL:REQ-CONTENT-020}}), REQ-CONTENT-040 ({{ISSUE_URL:REQ-CONTENT-040}}), REQ-NAV-020 ({{ISSUE_URL:REQ-NAV-020}})
- **Downstream Requirements**: None
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A

## Implementation Notes

- **Constraints**: Static site generator: TO BE DECIDED (PQ-16, ARCHITECTURE.md AQ-1); DEP-1 to DEP-8 apply to every dependency added; release gate: PDFs are not released while SEC-PRIV-4 (blocked) is open
- **Prohibited Approaches**: Indicating the PDF type by an icon alone without text
- **Implementation Guidance**: Include the indication in the link text so screen readers announce it.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`), because: Routine link list with a text label.
- **Required Human Review**: Product review by Nina (content and layout); design review against DESIGN.md and `style-guide.html`
- **Open Decisions**: Non-blocking: PQ-19 (placement), PQ-21, PQ-22.
