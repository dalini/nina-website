## Metadata

- **ID**: REQ-WORKS-050
- **Title**: Open a work's images in a full-screen image viewer with buttons and arrow keys, and fall back to plain links without JavaScript
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-3.6, FR-3.11, FR-3.7; DESIGN.md Components (Image viewer, Buttons, Focus state), Accessibility; SECURITY.md SEC-OUT-5; ARCHITECTURE.md Client framework; parent {{ISSUE_URL:REQ-WORKS-000}}

## Requirement

- **Statement**: Selecting an image on a work page MUST open it in the image viewer, which MUST show the selected image full-screen with its caption and credit, MUST move to the previous and next image by buttons and by the arrow keys, MUST close with Esc and a close button, MUST NOT advance on its own, MUST move focus into the viewer and back to the image when it closes, and MUST use no animated transition when reduced motion is preferred; without JavaScript, each image MUST be a plain link to its largest published size.
- **Rationale**: Visitors look at the work in detail (FR-3.11); the fallback keeps every image reachable without script.
- **Assumptions**: None
- **Out of Scope**: The image sizes (REQ-IMAGES-010); the image sequence on the page (REQ-WORKS-030); Exhibition installation images (FR-3.11 names work pages only).
- **Design Traceability**: DESIGN.md Components (Image viewer: full-screen on `background`, caption and credit below, Buttons for previous, next and close, focus handling, no auto-advance, reduced motion); Accessibility; `style-guide.html` as the rendered reference
- **Architecture Traceability**: Public Site (first-party vanilla JavaScript, ARCHITECTURE.md Client framework); Site Build
- **Security Traceability**: SEC-OUT-5; SEC-HTTP-2 (script only from the site's own files); SEC-OUT-7 (no inline script)

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Work page image links; the viewer's previous, next and close buttons; keyboard (arrow keys, Esc, Tab)
- **Actors**: Visitor (anonymous)
- **Preconditions**: REQ-WORKS-030, REQ-IMAGES-010
- **Data Classification**: Public
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Output Encoding
- **Threat References**: STRIDE Tampering: SECURITY.md Threat Model v3 T-32; CWE-79
- **Abuse / Misuse Case**: A crafted link with markup in the fragment or a caption with markup tries to run script through the viewer (DOM-based XSS)
- **Trust Boundary**: TB-1 (static pages served to visitors)
- **Untrusted Inputs or Assertions**: Values from the URL (query string, fragment), browser storage and page data read by the first-party script
- **Authoritative Enforcement Point**: Public Site first-party script (SEC-OUT-5)
- **Independent Verification**: Browser tests with hostile URL and storage values (SEC-OUT-5)
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: W3C WCAG 2.2, Level AA (DESIGN.md Accessibility target); OWASP XSS Prevention Cheat Sheet (`REF-XSS`)
- **Mapping Basis**: Other: WCAG 2.2 Level AA as targeted by DESIGN.md; `REF-XSS` as cited in SECURITY.md SEC-OUT-5.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given a work page with three images, when the visitor selects the second image, then the viewer opens it full-screen on `background` with its caption and credit below it, and focus moves into the viewer.
2. **AC-02 — Boundary or failure behavior**: Given the viewer shows the second of three images, when the visitor presses the right arrow key or the next button, then the third image is shown; the left arrow key and the previous button show the first; at either end the viewer never shows an image of another work; Esc and the close button close the viewer and focus returns to the image that opened it.
3. **AC-03 — Boundary or failure behavior**: Given JavaScript is disabled, when the visitor selects an image, then the browser opens that image's largest published size as a plain link.
4. **AC-04 — Prohibited behavior**: Given the viewer is open for one minute without input, when it is observed, then it never advances on its own, and with reduced motion preferred no animated transition runs.
5. **AC-05 — Additional criterion**: Given a caption and a URL fragment containing `<img src=x onerror=alert(1)>`, when the viewer shows the image, then the markup appears only as text and no script runs (SEC-OUT-5).

## Failure Behavior

- **On Invalid Input**: Content that violates the content model fails the build (REQ-CONTENT-030); nothing is published
- **On Authentication Failure**: N/A: public pages need no sign-in (NFR-2)
- **On Authorization Failure**: N/A: all public pages are read-only and need no account (NFR-2)
- **On Security-Decision Failure**: N/A: this page makes no security decision; encoding and link rules are enforced by REQ-OUTPUT-010 and REQ-OUTPUT-030
- **On External Dependency Failure**: N/A
- **On System Error**: The Site Build fails and publishes nothing; the previous site stays online (SEC-INPUT-3)
- **Logging / Audit**: The build log names the failing content item and field; it contains no secrets (SEC-SECRET-1)
- **Alerting**: A failed build emails Ives (REQ-PIPELINE-110); Nina sees it in the Editing Interface (REQ-EDIT-090)

## Test Strategy

- **Unit Tests**: Index arithmetic at the first and last image; text-only insertion of caption and credit
- **Integration Tests**: Browser tests on a built work page: open, previous, next, close, focus return, no-JavaScript fallback
- **Security Tests**: Hostile caption, credit, query string and fragment values (SEC-OUT-5); CSP violation check under REQ-HTTP-020
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:WORKS-050/AC-01`; AC-02 → `test:WORKS-050/AC-02`; AC-03 → `test:WORKS-050/AC-03`; AC-04 → `test:WORKS-050/AC-04`; AC-05 → `test:WORKS-050/AC-05` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model; the last two major versions of Chrome, Edge, Firefox, Safari (macOS, iOS) and Samsung Internet (NFR-4); keyboard-only and screen reader check

## Dependencies

- **Upstream Requirements**: REQ-WORKS-030 ({{ISSUE_URL:REQ-WORKS-030}}), REQ-IMAGES-010 ({{ISSUE_URL:REQ-IMAGES-010}})
- **Downstream Requirements**: REQ-PERF-010 ({{ISSUE_URL:REQ-PERF-010}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A: no runtime dependency; a failed build leaves the previous site online

## Implementation Notes

- **Constraints**: Small first-party vanilla JavaScript loaded from the site's own script file (ARCHITECTURE.md Client framework, SEC-HTTP-2, SEC-OUT-7); no client framework or library; DEP-1 to DEP-8 apply
- **Prohibited Approaches**: `innerHTML` or similar HTML insertion of any value (SEC-OUT-5); inline script or style attributes blocked by the CSP; auto-advancing slideshows
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: A small vanilla-JavaScript component fully described by FR-3.11 and DESIGN.md, with clear keyboard and focus tests.
- **Required Human Review**: Product review by Nina (content and layout); design review against DESIGN.md and `style-guide.html`
- **Open Decisions**: PQ-66 (non-blocking): whether next and previous stop or wrap around at the last and first image.
