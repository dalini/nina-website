## Metadata

- **ID**: REQ-WORKS-020
- **Title**: Show each work on its own page with title, year and, when entered, materials, dimensions, edition, series and text
- **Version**: 0.3.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-3.2, FR-3.3, FR-3.4, FR-3.5, FR-1.5; DESIGN.md Components (Work metadata), Typography; parent https://github.com/dalini/nina-website/issues/6

## Requirement

- **Statement**: The Site Build MUST generate one page per published work showing its title as entered and its year, and MUST show materials, dimensions (two or three values in centimeters, as entered), edition, series and short text only when they are entered.
- **Rationale**: Each work is presented with the information curators and collectors need (FR-3.2 to FR-3.5).
- **Assumptions**: None
- **Out of Scope**: Images (REQ-WORKS-030); links to exhibitions (REQ-WORKS-040); empty-language fallback (REQ-NAV-030).
- **Design Traceability**: DESIGN.md Components (Work metadata: title semibold, then year, materials, dimensions, edition in `secondary`, `sm`); Typography (work titles keep their casing); DESIGN.md Accessibility (WCAG 2.2 AA, visible focus, keyboard use, `lang`); `style-guide.html` as the rendered reference
- **Architecture Traceability**: Site Build (work pages from the Content Store)
- **Security Traceability**: SEC-OUT-1 (via REQ-OUTPUT-010)

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Work page (both languages)
- **Actors**: Visitor (anonymous)
- **Preconditions**: REQ-CONTENT-010, REQ-NAV-020, REQ-OUTPUT-010
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

1. **AC-01 — Expected behavior**: Given a published work `tiptoe`, 2018, with materials, three dimension values, edition `ed. 10`, series and short text, when the build runs, then its page shows all of them and the title exactly as `tiptoe`.
2. **AC-02 — Expected behavior**: Given a work with two dimension values, when its page is generated, then it shows exactly those two values with the unit cm, as entered.
3. **AC-03 — Boundary or failure behavior**: Given a work with no materials, dimensions, edition, series or short text, when its page is generated, then no label or empty element for them appears.
4. **AC-04 — Prohibited behavior**: Given any work, when its German and English pages are compared, then the title is identical and not translated, and no dimension value is converted or rounded.

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

- **Unit Tests**: Optional-field rendering (present, absent); dimension rendering for two and three values
- **Integration Tests**: Full build: one page per published work in both languages
- **Security Tests**: REQ-OUTPUT-010 hostile fixtures run against this page type; the REQ-PRIV-010 network test covers this page type
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:WORKS-020/AC-01`; AC-02 → `test:WORKS-020/AC-02`; AC-03 → `test:WORKS-020/AC-03`; AC-04 → `test:WORKS-020/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model; the last two major versions of Chrome, Edge, Firefox, Safari (macOS, iOS) and Samsung Internet (NFR-4); keyboard-only and screen reader check

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-010 (https://github.com/dalini/nina-website/issues/18), REQ-NAV-020 (https://github.com/dalini/nina-website/issues/32), REQ-OUTPUT-010 (https://github.com/dalini/nina-website/issues/27)
- **Downstream Requirements**: REQ-WORKS-030 (https://github.com/dalini/nina-website/issues/40), REQ-EXHIBITIONS-030 (https://github.com/dalini/nina-website/issues/47), REQ-WORKS-040 (https://github.com/dalini/nina-website/issues/48)
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A: no runtime dependency; a failed build leaves the previous site online

## Implementation Notes

- **Constraints**: Hugo (ARCHITECTURE.md Site Build); no client framework, and any first-party JavaScript follows SEC-OUT-5 (values inserted as text only); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Template text that editors are expected to change (DR-2); content rules evaluated in the visitor's browser (DR-3)
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Template rendering of defined fields with optional-field handling.
- **Required Human Review**: Product review by Nina (content and layout); design review against DESIGN.md and `style-guide.html`
- **Open Decisions**: None
