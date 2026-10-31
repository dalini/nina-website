## Metadata

- **ID**: REQ-WORKS-010
- **Title**: List all published works and show works in public space as a group
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-3.1, FR-3.8; DESIGN.md Layout and Spacing (Grid); parent {{ISSUE_URL:REQ-WORKS-000}}

## Requirement

- **Statement**: The Works page MUST list every published work with a link to its work page, MUST mark works flagged as in public space so they are identifiable, and MUST offer a view that shows exactly the published works in public space together.
- **Rationale**: Curators and the public browse the whole body of work and the public-space works as a group (FR-3.1, FR-3.8).
- **Assumptions**: None
- **Out of Scope**: Ordering of the list (FR-3.1: newest year first, manual order replaces it), responsive image sizes (NFR-1) and the featured work on the home page (FR-2): decided in step 10 and decomposed in the step 9 re-run.
- **Design Traceability**: DESIGN.md Layout and Spacing (works grid: 1 column on small screens, 2 from `md`, 3 from `lg`; image areas up to 1200 px); Components (Links); DESIGN.md Accessibility (WCAG 2.2 AA, visible focus, keyboard use, `lang`); `style-guide.html` as the rendered reference
- **Architecture Traceability**: Site Build (Works page from the Content Store); Content Store (public-space flag)
- **Security Traceability**: SEC-OUT-1 (via REQ-OUTPUT-010)

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Works page (DE, EN); public-space view
- **Actors**: Visitor (anonymous)
- **Preconditions**: REQ-CONTENT-010, REQ-CONTENT-040, REQ-NAV-020
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

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: W3C WCAG 2.2, Level AA (DESIGN.md Accessibility target)
- **Mapping Basis**: Other: WCAG 2.2 Level AA as targeted by DESIGN.md.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given three published works, one flagged in public space, when the Site Build runs, then the Works page lists all three with links to their work pages, and the flagged one carries a visible text marker that screen readers announce.
2. **AC-02 — Expected behavior**: Given the same content, when the visitor opens the public-space view, then it lists exactly the one flagged work.
3. **AC-03 — Boundary or failure behavior**: Given no published work is flagged in public space, when the public-space view is opened, then the page renders without errors and lists no work.
4. **AC-04 — Additional criterion**: Given viewport widths below 768 px, from 768 px and from 1024 px, when the Works page is shown, then the grid has 1, 2 and 3 columns respectively.
5. **AC-05 — Prohibited behavior**: Given an unpublished work flagged in public space, when either view is generated, then it does not appear (SEC-INPUT-4).

## Failure Behavior

- **On Invalid Input**: Content violating the model fails the build (REQ-CONTENT-030)
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication (SECURITY.md SEC-AUTHN-1 to -3) is decomposed in the step 9 re-run
- **On Authorization Failure**: N/A: all public pages are read-only and need no account (NFR-2)
- **On Security-Decision Failure**: N/A: this page makes no security decision; encoding and link rules are enforced by REQ-OUTPUT-010 and REQ-OUTPUT-030
- **On External Dependency Failure**: N/A
- **On System Error**: The Site Build fails and publishes nothing (SEC-INPUT-3); no partial page set is deployed
- **Logging / Audit**: Build log names the content item and field that failed; no secrets in the log (SEC-SECRET-1)
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: Selection of published and public-space works
- **Integration Tests**: Full build with fixture works; both views in both languages
- **Security Tests**: Covered by REQ-OUTPUT-010 fixtures run against this page type; REQ-PRIV-010 network test includes this page type
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:WORKS-010/AC-01`; AC-02 → `test:WORKS-010/AC-02`; AC-03 → `test:WORKS-010/AC-03`; AC-04 → `test:WORKS-010/AC-04`; AC-05 → `test:WORKS-010/AC-05`
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build run locally against fixture content in the documented content model; Static site generator: Hugo (ARCHITECTURE.md Site Build); the last two major versions of Chrome, Edge, Firefox, Safari (macOS, iOS) and Samsung Internet (NFR-4); screen reader check

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-010 ({{ISSUE_URL:REQ-CONTENT-010}}), REQ-CONTENT-040 ({{ISSUE_URL:REQ-CONTENT-040}}), REQ-NAV-020 ({{ISSUE_URL:REQ-NAV-020}})
- **Downstream Requirements**: None
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A

## Implementation Notes

- **Constraints**: Static site generator: Hugo (ARCHITECTURE.md Site Build); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Marking public-space works by color alone (DESIGN.md Accessibility)
- **Implementation Guidance**: Whether the public-space view is a filtered page or a section is a design detail; both meet FR-3.8.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`), because: List and filtered view from flags already in the model; layout fixed by DESIGN.md.
- **Required Human Review**: Product review by Nina (content and layout); design review against DESIGN.md and `style-guide.html`
- **Open Decisions**: None (step 10: browsers NFR-4; order FR-3.1).
