## Metadata

- **ID**: REQ-WORKS-020
- **Title**: Show each work on its own page with title, year, materials, dimensions and optional edition, series and text
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Repository maintainer (dalini); the maintainer role is still open in SECURITY.md SQ-10
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-3.2, FR-3.3, FR-3.4, FR-3.5, FR-1.5; DESIGN.md Components (Work metadata), Typography; parent {{ISSUE_URL:REQ-WORKS-000}}

## Requirement

- **Statement**: The Site Build MUST generate one page per published work showing its title as entered, year, materials and dimensions (two or three values in centimeters as entered), and MUST show the edition, series and short text only when they are entered.
- **Rationale**: Each work is presented with the information curators and collectors need (FR-3.2 to FR-3.5).
- **Assumptions**: None
- **Out of Scope**: Images (REQ-WORKS-030); links to exhibitions (FR-3.9, blocked by PQ-11); behavior when materials or dimensions are empty (blocked by PQ-13); empty language values (PQ-9).
- **Design Traceability**: DESIGN.md Components (Work metadata: title semibold, then year, materials, dimensions, edition in `secondary`, `sm`); Typography (work titles keep their original casing); DESIGN.md Accessibility (WCAG 2.2 AA, visible focus, keyboard use, `lang`); `style-guide.html` as the rendered reference
- **Architecture Traceability**: Site Build (work pages from the Content Store)
- **Security Traceability**: SEC-OUT-1 (via REQ-OUTPUT-010)

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Work page (DE, EN)
- **Actors**: Visitor (anonymous)
- **Preconditions**: REQ-CONTENT-010, REQ-NAV-020, REQ-OUTPUT-010
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

1. **AC-01 — Expected behavior**: Given a published work with title `tiptoe`, year, materials, three dimension values, edition `ed. 10`, series and short text, when the Site Build runs, then its page shows all of them, the title exactly as `tiptoe`.
2. **AC-02 — Expected behavior**: Given a work with two dimension values, when its page is generated, then it shows exactly those two values with the unit cm, as entered.
3. **AC-03 — Boundary or failure behavior**: Given a work with no edition, no series and no short text, when its page is generated, then no edition, series or text label or empty element appears.
4. **AC-04 — Prohibited behavior**: Given any work, when its German and English pages are compared, then the title is identical and not translated.

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

- **Unit Tests**: Optional-field rendering (present, absent); dimension rendering for two and three values
- **Integration Tests**: Full build: one page per published work in both languages
- **Security Tests**: Covered by REQ-OUTPUT-010 fixtures run against this page type; REQ-PRIV-010 network test includes this page type
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:WORKS-020/AC-01`; AC-02 → `test:WORKS-020/AC-02`; AC-03 → `test:WORKS-020/AC-03`; AC-04 → `test:WORKS-020/AC-04`
- **Coverage Target**: Project coverage target TO BE DECIDED (build and test commands are TO BE DECIDED in CLAUDE.md). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build run locally against fixture content in the documented content model; Static site generator: TO BE DECIDED (PQ-16, ARCHITECTURE.md AQ-1); current desktop and mobile browsers (list: PQ-21); screen reader check

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-010 ({{ISSUE_URL:REQ-CONTENT-010}}), REQ-NAV-020 ({{ISSUE_URL:REQ-NAV-020}}), REQ-OUTPUT-010 ({{ISSUE_URL:REQ-OUTPUT-010}})
- **Downstream Requirements**: REQ-WORKS-030 ({{ISSUE_URL:REQ-WORKS-030}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A

## Implementation Notes

- **Constraints**: Static site generator: TO BE DECIDED (PQ-16, ARCHITECTURE.md AQ-1); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Converting or rounding dimension values; changing title casing in CSS or templates
- **Implementation Guidance**: The exact dimension separator and order are not specified; show values as entered.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`), because: Template rendering of defined fields with optional-field handling.
- **Required Human Review**: Product review by Nina (content and layout); design review against DESIGN.md and `style-guide.html`
- **Open Decisions**: Non-blocking: PQ-21, PQ-24 (format of `year`).
