## Metadata

- **ID**: REQ-EXHIBITIONS-010
- **Title**: Show each Exhibition entry with title, type, venue, city, dates and, when entered, co-exhibitors, external link and linked works
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-4.2, FR-4.3, FR-4.5; parent {{ISSUE_URL:REQ-EXHIBITIONS-000}}

## Requirement

- **Statement**: Each published Exhibition entry MUST show its title, type (solo, two-person, group or fair), venue, city and dates; MUST show co-exhibitors and an external link when they are entered and omit them when empty; and MUST show its linked published works, each linking to its work page.
- **Rationale**: Curators and press need the facts of each show (FR-4.2) and the works shown (FR-4.5).
- **Assumptions**: None
- **Out of Scope**: Entries whose title, type, venue or city is empty (blocked scope, PQ-36); grouping (REQ-EXHIBITIONS-020).
- **Design Traceability**: DESIGN.md Components (Links; metadata in `secondary`); DESIGN.md Accessibility (WCAG 2.2 AA, visible focus, keyboard use, `lang`); `style-guide.html` as the rendered reference
- **Architecture Traceability**: Site Build; Public Site
- **Security Traceability**: SEC-OUT-2 (external link, via REQ-OUTPUT-030)

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Exhibition entry display on the Exhibitions/News page
- **Actors**: Visitor (anonymous)
- **Preconditions**: REQ-CONTENT-050, REQ-NAV-020, REQ-OUTPUT-010, REQ-OUTPUT-030, REQ-WORKS-020
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

1. **AC-01 — Expected behavior**: Given an entry with title, type group, venue, city, 2026-05-01 to 2026-06-30, two co-exhibitors, an external link and two linked works, when it is rendered, then all are shown and both works link to their pages.
2. **AC-02 — Boundary or failure behavior**: Given an entry without co-exhibitors and without external link, when it is rendered, then no label or empty element for them appears.
3. **AC-03 — Prohibited behavior**: Given an entry linked to an unpublished work, when it is rendered, then that work is not shown or linked.

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

- **Unit Tests**: Entry partial with optional fields; type label per language
- **Integration Tests**: Build with fixture entries
- **Security Tests**: REQ-OUTPUT-010 hostile fixtures run against this page type; the REQ-PRIV-010 network test covers this page type
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:EXHIBITIONS-010/AC-01`; AC-02 → `test:EXHIBITIONS-010/AC-02`; AC-03 → `test:EXHIBITIONS-010/AC-03` (test command TO BE DECIDED by the first coding PR, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model; the last two major versions of Chrome, Edge, Firefox, Safari (macOS, iOS) and Samsung Internet (NFR-4); keyboard-only and screen reader check

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-050 ({{ISSUE_URL:REQ-CONTENT-050}}), REQ-NAV-020 ({{ISSUE_URL:REQ-NAV-020}}), REQ-OUTPUT-010 ({{ISSUE_URL:REQ-OUTPUT-010}}), REQ-OUTPUT-030 ({{ISSUE_URL:REQ-OUTPUT-030}}), REQ-WORKS-020 ({{ISSUE_URL:REQ-WORKS-020}})
- **Downstream Requirements**: REQ-WORKS-040 ({{ISSUE_URL:REQ-WORKS-040}}), REQ-EXHIBITIONS-020 ({{ISSUE_URL:REQ-EXHIBITIONS-020}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A: no runtime dependency; a failed build leaves the previous site online

## Implementation Notes

- **Constraints**: Hugo (ARCHITECTURE.md Site Build); no client framework, and any first-party JavaScript follows SEC-OUT-5 (values inserted as text only); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Template text that editors are expected to change (DR-2); content rules evaluated in the visitor's browser (DR-3)
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; NOT VERIFIED, see ISSUE_PLAN.md Planning Rules), because: Template rendering of defined fields with optional-field handling.
- **Required Human Review**: Product review by Nina (content and layout); design review against DESIGN.md and `style-guide.html`
- **Open Decisions**: PQ-36 (blocking for the excluded part only); PQ-46 (non-blocking): German and English labels for the four types.
