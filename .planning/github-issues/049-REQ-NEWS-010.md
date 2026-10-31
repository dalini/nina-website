## Metadata

- **ID**: REQ-NEWS-010
- **Title**: List News items in their own section of the Exhibitions/News page, newest date first
- **Version**: 0.3.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 fourth run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-4.6, FR-3.7, FR-1.8; parent https://github.com/dalini/nina-website/issues/8

## Requirement

- **Statement**: News items MUST be listed in their own section of the Exhibitions/News page, newest date first, and News items of the same date alphabetically by title in the page's language; each MUST show its title and its date in the format of FR-1.8, and MUST show text, an external link and an image when they are entered.
- **Rationale**: News is separate from exhibitions (FR-4.6).
- **Assumptions**: None
- **Out of Scope**: Exhibition entries (REQ-EXHIBITIONS-010); the date format itself (REQ-NAV-040).
- **Design Traceability**: DESIGN.md Components (Image with credit, Links); DESIGN.md Accessibility (WCAG 2.2 AA, visible focus, keyboard use, `lang`); `style-guide.html` as the rendered reference
- **Architecture Traceability**: Site Build; Public Site
- **Security Traceability**: SEC-OUT-1 (News text is rich text), SEC-OUT-2 (via REQ-OUTPUT-010, REQ-OUTPUT-030)

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: News section of the Exhibitions/News page
- **Actors**: Visitor (anonymous)
- **Preconditions**: REQ-EXHIBITIONS-020, REQ-OUTPUT-010, REQ-OUTPUT-030, REQ-IMAGES-010, REQ-NAV-040
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

1. **AC-01 — Expected behavior**: Given News items dated 2026-03-01 and 2026-05-01, when the page is generated, then 2026-05-01 is listed first in the News section, shown as `2026-05-01` on the English page and `01.05.2026` on the German page.
2. **AC-02 — Boundary or failure behavior**: Given a News item with only title and date, when it is rendered, then no text, link or image element appears for it.
3. **AC-03 — Boundary or failure behavior**: Given a News item with an image, when it is rendered, then the image has its alt text and uses generated sizes (REQ-IMAGES-010).
4. **AC-04 — Prohibited behavior**: Given News items, when the page is generated, then none appears inside the upcoming, current or past exhibition groups.
5. **AC-05 — Additional criterion**: Given two News items dated 2026-05-01 titled `Zeitung` and `Atelier`, when the page is generated, then `Atelier` is listed before `Zeitung`.

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

- **Unit Tests**: Sort by date; optional fields
- **Integration Tests**: Build with fixture News items
- **Security Tests**: REQ-OUTPUT-010 hostile fixtures run against this page type; the REQ-PRIV-010 network test covers this page type
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:NEWS-010/AC-01`; AC-02 → `test:NEWS-010/AC-02`; AC-03 → `test:NEWS-010/AC-03`; AC-04 → `test:NEWS-010/AC-04`; AC-05 → `test:NEWS-010/AC-05` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model; the last two major versions of Chrome, Edge, Firefox, Safari (macOS, iOS) and Samsung Internet (NFR-4); keyboard-only and screen reader check

## Dependencies

- **Upstream Requirements**: REQ-EXHIBITIONS-020 (https://github.com/dalini/nina-website/issues/49), REQ-OUTPUT-010 (https://github.com/dalini/nina-website/issues/27), REQ-OUTPUT-030 (https://github.com/dalini/nina-website/issues/29), REQ-IMAGES-010 (https://github.com/dalini/nina-website/issues/37), REQ-NAV-040 (https://github.com/dalini/nina-website/issues/33)
- **Downstream Requirements**: REQ-PERF-010 (https://github.com/dalini/nina-website/issues/72)
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A: no runtime dependency; a failed build leaves the previous site online

## Implementation Notes

- **Constraints**: Hugo (ARCHITECTURE.md Site Build); no client framework, and any first-party JavaScript follows SEC-OUT-5 (values inserted as text only); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Template text that editors are expected to change (DR-2); content rules evaluated in the visitor's browser (DR-3)
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Routine list with optional fields.
- **Required Human Review**: Product review by Nina (content and layout); design review against DESIGN.md and `style-guide.html`
- **Open Decisions**: None. Decided in step 10 a third time (`.planning/OPEN_QUESTIONS.md`): PQ-65: numeric dates: DE `DD.MM.YYYY`, EN `YYYY-MM-DD`; ranges with both full dates and an en dash (FR-1.8); applied through REQ-NAV-040.
