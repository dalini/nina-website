## Metadata

- **ID**: REQ-HOME-010
- **Title**: Show the artist's name and the featured work's image, linked to its work page, on the home page
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-2.1, FR-2.2, FR-10.3, FR-10.6; parent {{ISSUE_URL:REQ-EPIC-001}}

## Requirement

- **Statement**: The home page MUST show the artist's name and one large image of the featured work, and that image MUST link to the featured work's page.
- **Rationale**: The home page leads with one work (FR-2.1).
- **Assumptions**: None
- **Out of Scope**: The Exhibition entry (REQ-HOME-020); the Instagram posts (REQ-INSTAGRAM-020).
- **Design Traceability**: DESIGN.md Layout and Spacing (image areas up to 1200 px, no cropping on work pages), Components (Image with credit); DESIGN.md Accessibility (WCAG 2.2 AA, visible focus, keyboard use, `lang`); `style-guide.html` as the rendered reference
- **Architecture Traceability**: Site Build; Public Site
- **Security Traceability**: N/A

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Home page (`/` and `/de/`)
- **Actors**: Visitor (anonymous)
- **Preconditions**: REQ-CONTENT-010, REQ-CONTENT-030, REQ-NAV-020, REQ-IMAGES-010
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

1. **AC-01 — Expected behavior**: Given `vom Flüstern und Formen` is the featured published work, when the home page is generated, then it shows "Nina Laaf" and that work's first image, linking to its work page in the same language.
2. **AC-02 — Boundary or failure behavior**: Given the featured work is changed to another work, when the site is rebuilt, then the home page shows the new work's image and link only.
3. **AC-03 — Boundary or failure behavior**: Given no published work is featured, when the Site Build runs, then it fails under REQ-CONTENT-030 and the previous home page stays online.
4. **AC-04 — Prohibited behavior**: Given the home page, when it is inspected, then the featured image is served in responsive sizes (REQ-IMAGES-010), has alt text, and is not the original file.

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

- **Unit Tests**: Featured-work lookup
- **Integration Tests**: Build with fixture content; home pages in both languages
- **Security Tests**: REQ-OUTPUT-010 hostile fixtures run against this page type; the REQ-PRIV-010 network test covers this page type
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:HOME-010/AC-01`; AC-02 → `test:HOME-010/AC-02`; AC-03 → `test:HOME-010/AC-03`; AC-04 → `test:HOME-010/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model; the last two major versions of Chrome, Edge, Firefox, Safari (macOS, iOS) and Samsung Internet (NFR-4); keyboard-only and screen reader check

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-010 ({{ISSUE_URL:REQ-CONTENT-010}}), REQ-CONTENT-030 ({{ISSUE_URL:REQ-CONTENT-030}}), REQ-NAV-020 ({{ISSUE_URL:REQ-NAV-020}}), REQ-IMAGES-010 ({{ISSUE_URL:REQ-IMAGES-010}})
- **Downstream Requirements**: REQ-HOME-020 ({{ISSUE_URL:REQ-HOME-020}}), REQ-PERF-010 ({{ISSUE_URL:REQ-PERF-010}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A: no runtime dependency; a failed build leaves the previous site online

## Implementation Notes

- **Constraints**: Hugo (ARCHITECTURE.md Site Build); no client framework, and any first-party JavaScript follows SEC-OUT-5 (values inserted as text only); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Template text that editors are expected to change (DR-2); content rules evaluated in the visitor's browser (DR-3)
- **Implementation Guidance**: 'The first image' of the featured work is the image Nina ordered first (FR-3.6).
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: A single template section driven by one content reference.
- **Required Human Review**: Product review by Nina (content and layout); design review against DESIGN.md and `style-guide.html`
- **Open Decisions**: None
