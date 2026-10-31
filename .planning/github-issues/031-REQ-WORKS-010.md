## Metadata

- **ID**: REQ-WORKS-010
- **Title**: List all published works newest year first or in Nina's order, and show works in public space as a group
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-3.1, FR-3.8, FR-10.3; DESIGN.md Layout and Spacing (Grid); parent {{ISSUE_URL:REQ-WORKS-000}}

## Requirement

- **Statement**: The Works page MUST list all published works, newest year first, unless Nina has set a manual order, which then replaces it; works marked "in public space" MUST be identifiable on the Works page and MUST be viewable together as a group.
- **Rationale**: Curators browse the whole body of work and the public commissions (FR-3.1, FR-3.8).
- **Assumptions**: None
- **Out of Scope**: Order of works within the same year (PQ-49, non-blocking); work pages (REQ-WORKS-020).
- **Design Traceability**: DESIGN.md Layout and Spacing (Grid: 1 column small, 2 from `md`, 3 from `lg`); DESIGN.md Accessibility (WCAG 2.2 AA, visible focus, keyboard use, `lang`); `style-guide.html` as the rendered reference
- **Architecture Traceability**: Site Build; Public Site
- **Security Traceability**: SEC-INPUT-4 (via REQ-CONTENT-040)

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Works page; public-space group view (both languages)
- **Actors**: Visitor (anonymous)
- **Preconditions**: REQ-CONTENT-010, REQ-CONTENT-040, REQ-NAV-020, REQ-IMAGES-010
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

1. **AC-01 — Expected behavior**: Given published works from 2016, 2023 and 2019 and no manual order, when the Works page is generated, then they appear 2023, 2019, 2016.
2. **AC-02 — Expected behavior**: Given Nina set a manual order placing the 2016 work first, when the Works page is generated, then that order is used instead of year order.
3. **AC-03 — Boundary or failure behavior**: Given three works marked in public space and five not, when the site is built, then the Works page marks the three, and the group view lists exactly those three.
4. **AC-04 — Prohibited behavior**: Given an unpublished work, when the Works page and group view are generated, then it appears in neither.
5. **AC-05 — Additional criterion**: Given screen widths of 400, 800 and 1100 px, when the Works page is shown, then the grid has 1, 2 and 3 columns.

## Failure Behavior

- **On Invalid Input**: Content that violates the content model fails the build (REQ-CONTENT-030); nothing is published
- **On Authentication Failure**: N/A: public pages need no sign-in (NFR-2)
- **On Authorization Failure**: N/A: all public pages are read-only and need no account (NFR-2)
- **On Security-Decision Failure**: N/A: this page makes no security decision; encoding and link rules are enforced by REQ-OUTPUT-010 and REQ-OUTPUT-030
- **On External Dependency Failure**: N/A
- **On System Error**: The Site Build fails and publishes nothing; the previous site stays online (SEC-INPUT-3)
- **Logging / Audit**: The build log names the failing content item and field; it contains no secrets (SEC-SECRET-1)
- **Alerting**: A failed build emails Ives (REQ-PIPELINE-110)

## Test Strategy

- **Unit Tests**: Sort function (year, manual order); public-space filter
- **Integration Tests**: Build with fixture works; both pages in both languages
- **Security Tests**: REQ-OUTPUT-010 hostile fixtures run against this page type; the REQ-PRIV-010 network test covers this page type
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:WORKS-010/AC-01`; AC-02 → `test:WORKS-010/AC-02`; AC-03 → `test:WORKS-010/AC-03`; AC-04 → `test:WORKS-010/AC-04`; AC-05 → `test:WORKS-010/AC-05` (test command TO BE DECIDED by the first coding PR, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model; the last two major versions of Chrome, Edge, Firefox, Safari (macOS, iOS) and Samsung Internet (NFR-4); keyboard-only and screen reader check

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-010 ({{ISSUE_URL:REQ-CONTENT-010}}), REQ-CONTENT-040 ({{ISSUE_URL:REQ-CONTENT-040}}), REQ-NAV-020 ({{ISSUE_URL:REQ-NAV-020}}), REQ-IMAGES-010 ({{ISSUE_URL:REQ-IMAGES-010}})
- **Downstream Requirements**: REQ-PERF-010 ({{ISSUE_URL:REQ-PERF-010}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A: no runtime dependency; a failed build leaves the previous site online

## Implementation Notes

- **Constraints**: Hugo (ARCHITECTURE.md Site Build); no client framework, and any first-party JavaScript follows SEC-OUT-5 (values inserted as text only); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Template text that editors are expected to change (DR-2); content rules evaluated in the visitor's browser (DR-3)
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; NOT VERIFIED, see ISSUE_PLAN.md Planning Rules), because: List and filtered view from fields already in the model; layout fixed by DESIGN.md.
- **Required Human Review**: Product review by Nina (content and layout); design review against DESIGN.md and `style-guide.html`
- **Open Decisions**: PQ-49 (non-blocking): order within one year.
