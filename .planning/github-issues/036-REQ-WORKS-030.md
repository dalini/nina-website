## Metadata

- **ID**: REQ-WORKS-030
- **Title**: Show all of a work's images in Nina's order with alt text, caption and photo credit, without watermarks or save blocking
- **Version**: 0.3.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-3.6, FR-3.7, FR-3.10; DESIGN.md Components (Image with credit), Layout and Spacing, Accessibility; parent {{ISSUE_URL:REQ-WORKS-000}}

## Requirement

- **Statement**: A work page MUST show all images of the work in the order Nina sets, each with its alt text and, when entered, its caption (plain text in German and English, shown below the image) and photo credit ("Foto: …" / "Photo: …"); images MUST keep their aspect ratio and not be cropped; the site MUST NOT add watermarks or block saving images.
- **Rationale**: Images carry the work (FR-3.6); alt text and credits are required (FR-3.7); saving is not blocked (FR-3.10).
- **Assumptions**: None
- **Out of Scope**: Producing the image sizes (REQ-IMAGES-010); the image viewer (REQ-WORKS-050).
- **Design Traceability**: DESIGN.md Components (Image with credit: caption and credit in `secondary`, `xs`); Layout and Spacing (never cropped on work pages); DESIGN.md Accessibility (WCAG 2.2 AA, visible focus, keyboard use, `lang`); `style-guide.html` as the rendered reference
- **Architecture Traceability**: Site Build; Public Site
- **Security Traceability**: N/A

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Work page image sequence
- **Actors**: Visitor (anonymous)
- **Preconditions**: REQ-WORKS-020, REQ-IMAGES-010
- **Data Classification**: Public
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

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
- **Regulatory**: N/A
- **Other**: W3C WCAG 2.2, Level AA (DESIGN.md Accessibility target)
- **Mapping Basis**: Other: WCAG 2.2 Level AA as targeted by DESIGN.md.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given a work with images ordered 3, 1, 2 by Nina, when its page is generated, then they appear in that order, each with its alt text.
2. **AC-02 — Boundary or failure behavior**: Given an image with credit `Max Muster` and one without credit, when the German and English pages are generated, then the first shows "Foto: Max Muster" / "Photo: Max Muster" and the second shows no credit line.
3. **AC-03 — Prohibited behavior**: Given any work page, when it is inspected, then no watermark overlay, no right-click or drag blocking script or CSS, and no cropping of an image is present.
4. **AC-04 — Additional criterion**: Given a screen reader user, when the image sequence is read, then each image's alt text is announced in order.
5. **AC-05 — Additional criterion**: Given an image with a German caption only, when the English page is generated, then the German caption is shown below the image inside an element with `lang="de"` and no placeholder text (FR-1.7).

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

- **Unit Tests**: Image ordering; caption and credit per language, including the FR-1.7 fallback
- **Integration Tests**: Build with multi-image fixture works
- **Security Tests**: REQ-OUTPUT-010 hostile fixtures run against this page type; the REQ-PRIV-010 network test covers this page type
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:WORKS-030/AC-01`; AC-02 → `test:WORKS-030/AC-02`; AC-03 → `test:WORKS-030/AC-03`; AC-04 → `test:WORKS-030/AC-04`; AC-05 → `test:WORKS-030/AC-05` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model; the last two major versions of Chrome, Edge, Firefox, Safari (macOS, iOS) and Samsung Internet (NFR-4); keyboard-only and screen reader check

## Dependencies

- **Upstream Requirements**: REQ-WORKS-020 ({{ISSUE_URL:REQ-WORKS-020}}), REQ-IMAGES-010 ({{ISSUE_URL:REQ-IMAGES-010}})
- **Downstream Requirements**: REQ-WORKS-050 ({{ISSUE_URL:REQ-WORKS-050}}), REQ-PERF-010 ({{ISSUE_URL:REQ-PERF-010}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A: no runtime dependency; a failed build leaves the previous site online

## Implementation Notes

- **Constraints**: Hugo (ARCHITECTURE.md Site Build); no client framework, and any first-party JavaScript follows SEC-OUT-5 (values inserted as text only); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Template text that editors are expected to change (DR-2); content rules evaluated in the visitor's browser (DR-3)
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Ordered image rendering with labels; the image processing itself is a separate issue.
- **Required Human Review**: Product review by Nina (content and layout); design review against DESIGN.md and `style-guide.html`
- **Open Decisions**: None
