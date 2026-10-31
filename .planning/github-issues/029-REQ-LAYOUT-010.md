## Metadata

- **ID**: REQ-LAYOUT-010
- **Title**: Implement the design tokens, base page layout, light and dark mode, header wordmark and favicon
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: DESIGN.md Brand and Logo, Color Palette, Typography, Layout and Spacing, Components (Links, Buttons, Focus state), Accessibility; REQUIREMENTS.md NFR-1, NFR-4; SECURITY.md SEC-PRIV-1; parent https://github.com/dalini/nina-website/issues/2

## Requirement

- **Statement**: The Site Build MUST produce a shared page layout that uses the DESIGN.md tokens for color (light by default, dark when the visitor's system prefers dark), typography (the system UI stack, no font files), spacing, breakpoints and content widths; the header MUST show the wordmark "Nina Laaf" in weight 600 and the mark from `logo.svg` MUST be the favicon; links, buttons and focus states MUST follow DESIGN.md Components; with reduced motion preferred, no animated transitions are used.
- **Rationale**: Every page type builds on one layout so the design language and accessibility rules hold everywhere.
- **Assumptions**: None
- **Out of Scope**: Navigation links (REQ-NAV-010); language switch (REQ-NAV-020); footer links (REQ-LEGAL-010, REQ-INSTAGRAM-010); the final logo mark (deferred by Ives; `logo.svg` stays provisional).
- **Design Traceability**: DESIGN.md Brand and Logo, Color Palette, Typography, Layout and Spacing, Components; DESIGN.md Accessibility (WCAG 2.2 AA, visible focus, keyboard use, `lang`); `style-guide.html` as the rendered reference
- **Architecture Traceability**: Site Build (templates and design tokens in `dalini/nina-site`, DR-8); Public Site
- **Security Traceability**: SEC-PRIV-1 (no font files or third-party assets)

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Base template, stylesheet, favicon
- **Actors**: Visitor (anonymous)
- **Preconditions**: None
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

1. **AC-01 — Expected behavior**: Given any generated page, when it is opened with the system in light mode and then in dark mode, then the background, text, primary and secondary colors match the DESIGN.md Light and Dark columns.
2. **AC-02 — Expected behavior**: Given any page, when it loads, then the header shows only the wordmark "Nina Laaf" in weight 600, and the favicon is the mark from `logo.svg`.
3. **AC-03 — Boundary or failure behavior**: Given a keyboard user tabbing through a page, when each link or button receives focus, then a 2 px `primary` outline with 2 px offset is visible in both modes.
4. **AC-04 — Prohibited behavior**: Given any page, when its network requests are recorded, then no font file and no third-party resource is requested.
5. **AC-05 — Additional criterion**: Given `prefers-reduced-motion: reduce`, when a page is used, then no animated transition runs.

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

- **Unit Tests**: N/A: templates and styles; checked by the integration and browser tests
- **Integration Tests**: Build and render every page type in both color modes; contrast check of the token pairs listed in DESIGN.md
- **Security Tests**: Network recording: no font or third-party request (SEC-PRIV-1)
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:LAYOUT-010/AC-01`; AC-02 → `test:LAYOUT-010/AC-02`; AC-03 → `test:LAYOUT-010/AC-03`; AC-04 → `test:LAYOUT-010/AC-04`; AC-05 → `test:LAYOUT-010/AC-05` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model; the last two major versions of Chrome, Edge, Firefox, Safari (macOS, iOS) and Samsung Internet (NFR-4); keyboard-only and screen reader check

## Dependencies

- **Upstream Requirements**: None
- **Downstream Requirements**: REQ-NAV-020 (https://github.com/dalini/nina-website/issues/32), REQ-INSTAGRAM-010 (https://github.com/dalini/nina-website/issues/59), REQ-CONSENT-010 (https://github.com/dalini/nina-website/issues/61), REQ-PRIV-010 (https://github.com/dalini/nina-website/issues/73)
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A: no runtime dependency; a failed build leaves the previous site online

## Implementation Notes

- **Constraints**: Hugo (ARCHITECTURE.md Site Build); no client framework, and any first-party JavaScript follows SEC-OUT-5 (values inserted as text only); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Template text that editors are expected to change (DR-2); content rules evaluated in the visitor's browser (DR-3)
- **Implementation Guidance**: Keep tokens in one stylesheet in `dalini/nina-site`; no inline styles, so the CSP of SEC-HTTP-2 (`style-src 'self'`) holds.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Translating a complete token table and component list into one stylesheet and base template is well specified.
- **Required Human Review**: Product review by Nina (content and layout); design review against DESIGN.md and `style-guide.html`
- **Open Decisions**: None. The final logo mark is deferred by Ives (DESIGN.md); the favicon uses the provisional `logo.svg`.
