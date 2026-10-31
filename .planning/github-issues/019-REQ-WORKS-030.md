## Metadata

- **ID**: REQ-WORKS-030
- **Title**: Show a work's images in Nina's order with alt text and photo credit
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Repository maintainer (dalini); the maintainer role is still open in SECURITY.md SQ-10
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-3.6, FR-3.7; DESIGN.md Components (Image with credit), Layout and Spacing, Accessibility; parent {{ISSUE_URL:REQ-WORKS-000}}

## Requirement

- **Statement**: A work page MUST show all images of the work in the order stored in the Content Store, each with its alt text, and MUST show an image's photo credit when one is entered.
- **Rationale**: Object and installation views are the core of the site (FR-3.6); alt text and credits are required for accessibility and attribution (FR-3.7).
- **Assumptions**: None
- **Out of Scope**: Responsive image sizes and image processing (NFR-1, SEC-INPUT-5; blocked by PQ-5); removal of image metadata (SEC-PRIV-4; blocked by PQ-5, PQ-6); image protection (OQ-9, PQ-5).
- **Design Traceability**: DESIGN.md Components (Image with credit: caption and credit "Foto: …" / "Photo: …" in `secondary`, `xs`); Layout and Spacing (images keep their aspect ratio, never cropped on work pages); Accessibility (alt text, reduced motion: no auto-advancing images); DESIGN.md Accessibility (WCAG 2.2 AA, visible focus, keyboard use, `lang`); `style-guide.html` as the rendered reference
- **Architecture Traceability**: Site Build (work pages); Content Store (image order, alt text, credits)
- **Security Traceability**: SEC-OUT-1 (via REQ-OUTPUT-010)

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Work page image sequence (DE, EN)
- **Actors**: Visitor (anonymous)
- **Preconditions**: REQ-WORKS-020
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

1. **AC-01 — Expected behavior**: Given a work with three images stored in the order B, A, C, when its page is generated, then the images appear in the order B, A, C, each with its alt text in the page's language.
2. **AC-02 — Expected behavior**: Given an image with a photo credit, when its page is generated, then the credit appears after the image as "Foto: …" on German pages and "Photo: …" on English pages.
3. **AC-03 — Boundary or failure behavior**: Given an image without a photo credit, when its page is generated, then no credit label or empty element appears.
4. **AC-04 — Additional criterion**: Given images of different aspect ratios, when the page is shown at any breakpoint, then no image is cropped and keyboard users can reach every image sequence control, if any exists.
5. **AC-05 — Prohibited behavior**: Given any work page, when it is inspected, then no image lacks an `alt` attribute and no image sequence advances automatically.

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

- **Unit Tests**: Image ordering; credit label per language
- **Integration Tests**: Full build: image order, alt text and credits on every work page
- **Security Tests**: Covered by REQ-OUTPUT-010 fixtures run against this page type; REQ-PRIV-010 network test includes this page type
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:WORKS-030/AC-01`; AC-02 → `test:WORKS-030/AC-02`; AC-03 → `test:WORKS-030/AC-03`; AC-04 → `test:WORKS-030/AC-04`; AC-05 → `test:WORKS-030/AC-05`
- **Coverage Target**: Project coverage target TO BE DECIDED (build and test commands are TO BE DECIDED in CLAUDE.md). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build run locally against fixture content in the documented content model; Static site generator: TO BE DECIDED (PQ-16, ARCHITECTURE.md AQ-1); current desktop and mobile browsers (list: PQ-21); screen reader check

## Dependencies

- **Upstream Requirements**: REQ-WORKS-020 ({{ISSUE_URL:REQ-WORKS-020}})
- **Downstream Requirements**: None
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A

## Implementation Notes

- **Constraints**: Static site generator: TO BE DECIDED (PQ-16, ARCHITECTURE.md AQ-1); DEP-1 to DEP-8 apply to every dependency added; release gate: the site is not released while NFR-1 and SEC-PRIV-4 (blocked by PQ-5, PQ-6) are open, because this issue alone would publish original image files
- **Prohibited Approaches**: Cropping images on work pages; carousels that auto-advance
- **Implementation Guidance**: Use the image references the build provides; when the blocked image workstream lands, only the source of those references changes.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`), because: Ordered image rendering with alt text and credit labels; no image processing in scope.
- **Required Human Review**: Product review by Nina (content and layout); design review against DESIGN.md and `style-guide.html`
- **Open Decisions**: Non-blocking: PQ-21. Blocked parts listed in Out of Scope (PQ-5, PQ-6).
