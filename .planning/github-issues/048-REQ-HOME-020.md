## Metadata

- **ID**: REQ-HOME-020
- **Title**: Show the current, else the next upcoming, else the most recent past Exhibition entry on the home page
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-2.3, FR-4.1, FR-10.4; parent {{ISSUE_URL:REQ-EPIC-001}}

## Requirement

- **Statement**: The home page MUST show one published Exhibition entry: the current one; if none is current, the next upcoming one; if none is upcoming, the most recent past one; when several qualify, the order of FR-4.1 decides; when no published Exhibition entry exists, the home page MUST omit the exhibition block.
- **Rationale**: Visitors see what is on now or next (FR-2.3).
- **Assumptions**: None
- **Out of Scope**: The daily rebuild that keeps the choice current (REQ-PIPELINE-080).
- **Design Traceability**: DESIGN.md Components (Work metadata styles reused for entry metadata), Layout and Spacing; DESIGN.md Accessibility (WCAG 2.2 AA, visible focus, keyboard use, `lang`); `style-guide.html` as the rendered reference
- **Architecture Traceability**: Site Build (home page entry, FR-2.3); Public Site
- **Security Traceability**: N/A

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Home page (`/` and `/de/`)
- **Actors**: Visitor (anonymous)
- **Preconditions**: REQ-HOME-010, REQ-EXHIBITIONS-020
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

1. **AC-01 — Expected behavior**: Given today (Europe/Berlin) is 2026-06-10 and entries A (current, start 06-01) and B (current, start 05-15) exist, when the home page is generated, then it shows B (earliest start first, FR-4.1).
2. **AC-02 — Boundary or failure behavior**: Given no current entry and upcoming entries starting 07-01 and 08-01, when the home page is generated, then it shows the 07-01 entry.
3. **AC-03 — Boundary or failure behavior**: Given only past entries ending 2025-12-01 and 2026-03-01, when the home page is generated, then it shows the 2026-03-01 entry.
4. **AC-04 — Prohibited behavior**: Given an unpublished current entry, when the home page is generated, then it is never shown.
5. **AC-05 — Additional criterion**: Given no published Exhibition entry, when the home page is generated, then it has no exhibition block or empty heading, the Instagram posts follow the featured image, and the build succeeds (FR-2.3, FR-2.4).

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

- **Unit Tests**: Selection function over grouped entries with a fixed 'today'
- **Integration Tests**: Builds with different fixed build dates
- **Security Tests**: REQ-OUTPUT-010 hostile fixtures run against this page type; the REQ-PRIV-010 network test covers this page type
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:HOME-020/AC-01`; AC-02 → `test:HOME-020/AC-02`; AC-03 → `test:HOME-020/AC-03`; AC-04 → `test:HOME-020/AC-04`; AC-05 → `test:HOME-020/AC-05` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model; the last two major versions of Chrome, Edge, Firefox, Safari (macOS, iOS) and Samsung Internet (NFR-4); keyboard-only and screen reader check

## Dependencies

- **Upstream Requirements**: REQ-HOME-010 ({{ISSUE_URL:REQ-HOME-010}}), REQ-EXHIBITIONS-020 ({{ISSUE_URL:REQ-EXHIBITIONS-020}})
- **Downstream Requirements**: REQ-INSTAGRAM-020 ({{ISSUE_URL:REQ-INSTAGRAM-020}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A: no runtime dependency; a failed build leaves the previous site online

## Implementation Notes

- **Constraints**: Hugo (ARCHITECTURE.md Site Build); no client framework, and any first-party JavaScript follows SEC-OUT-5 (values inserted as text only); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Template text that editors are expected to change (DR-2); content rules evaluated in the visitor's browser (DR-3)
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: A small selection rule on top of the grouping in REQ-EXHIBITIONS-020.
- **Required Human Review**: Product review by Nina (content and layout); design review against DESIGN.md and `style-guide.html`
- **Open Decisions**: None
