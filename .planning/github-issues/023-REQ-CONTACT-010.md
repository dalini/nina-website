## Metadata

- **ID**: REQ-CONTACT-010
- **Title**: Show Nina's email address as a mailto link and link to galerie burster on the Contact page
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Repository maintainer (dalini); the maintainer role is still open in SECURITY.md SQ-10
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-7.1, FR-7.2; parent {{ISSUE_URL:REQ-EPIC-001}}

## Requirement

- **Statement**: The Contact page MUST show Nina's email address from the Contact Page content as a `mailto:` link and MUST show a link to galerie burster.
- **Rationale**: Contacting Nina is a core purpose of the site (Project purpose; FR-7.1, FR-7.2).
- **Assumptions**: None
- **Out of Scope**: Whether galerie burster is named as the contact for sales (OQ-5, PQ-26: Page content wording); behavior when the email address is missing (PQ-13); a contact form (FR-7.3, not in this release).
- **Design Traceability**: DESIGN.md Components (Links); DESIGN.md Accessibility (WCAG 2.2 AA, visible focus, keyboard use, `lang`); `style-guide.html` as the rendered reference
- **Architecture Traceability**: Content Store (Page: Contact), Site Build, Public Site
- **Security Traceability**: SEC-OUT-2 (via REQ-OUTPUT-030); SEC-OUT-1

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Contact page (DE, EN)
- **Actors**: Visitor (anonymous)
- **Preconditions**: REQ-CONTENT-020, REQ-NAV-020, REQ-OUTPUT-030
- **Data Classification**: Public
- **Personal or Regulated Data**: Personal Data (Nina's email address, published on purpose; SECURITY.md T-42 accepted)
- **Jurisdiction / Regulatory Scope**: Germany / EU: GDPR (published contact data of the operator)

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Output Encoding
- **Threat References**: STRIDE Information Disclosure: SECURITY.md Threat Model v1 T-42 (accepted: address published on purpose)
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

1. **AC-01 — Expected behavior**: Given Contact Page content with an email address and the galerie burster link, when the Site Build runs, then the Contact page in both languages shows the address as a link whose target is `mailto:` plus that address, and shows the galerie burster link.
2. **AC-02 — Boundary or failure behavior**: Given Contact Page content whose galerie burster link uses `http:`, when the Site Build runs, then the build fails naming the Contact page and the link field (SEC-OUT-2, SEC-INPUT-3).
3. **AC-03 — Prohibited behavior**: Given the generated Contact page, when it is inspected, then the email address and the gallery link come from Page content and not from template text (DR-2).

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

- **Unit Tests**: mailto link construction from the content value
- **Integration Tests**: Full build with Contact fixtures
- **Security Tests**: Covered by REQ-OUTPUT-010 fixtures run against this page type; REQ-PRIV-010 network test includes this page type
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:CONTACT-010/AC-01`; AC-02 → `test:CONTACT-010/AC-02`; AC-03 → `test:CONTACT-010/AC-03`
- **Coverage Target**: Project coverage target TO BE DECIDED (build and test commands are TO BE DECIDED in CLAUDE.md). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build run locally against fixture content in the documented content model; Static site generator: TO BE DECIDED (PQ-16, ARCHITECTURE.md AQ-1); current desktop and mobile browsers (list: PQ-21); screen reader check

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-020 ({{ISSUE_URL:REQ-CONTENT-020}}), REQ-NAV-020 ({{ISSUE_URL:REQ-NAV-020}}), REQ-OUTPUT-030 ({{ISSUE_URL:REQ-OUTPUT-030}})
- **Downstream Requirements**: None
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A

## Implementation Notes

- **Constraints**: Static site generator: TO BE DECIDED (PQ-16, ARCHITECTURE.md AQ-1); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Obfuscation that needs third-party script (SEC-PRIV-1); client-side-only rendering of the address
- **Implementation Guidance**: Build the `mailto:` target from the stored address; the link text shows the address itself.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`), because: Single page with two content-driven links.
- **Required Human Review**: Product review by Nina (content and layout); design review against DESIGN.md and `style-guide.html`
- **Open Decisions**: Non-blocking: PQ-21, PQ-26.
