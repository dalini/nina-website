## Metadata

- **ID**: REQ-EXHIBITIONS-020
- **Title**: Group Exhibition entries as upcoming, current and past by the Europe/Berlin date and keep past entries as an archive
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-4.1, FR-4.4, FR-10.4; parent {{ISSUE_URL:REQ-EXHIBITIONS-000}}

## Requirement

- **Statement**: The Exhibitions/News page MUST list published Exhibition entries in the groups upcoming (start after today), current (today between start and end date, both inclusive) and past (end before today), with today taken as the build date in Europe/Berlin; upcoming and current entries are listed earliest start first, past entries most recent end date first; past entries MUST remain listed.
- **Rationale**: FR-4.1 defines the grouping exactly; the nightly build keeps it current (FR-10.4).
- **Assumptions**: None
- **Out of Scope**: Entries whose end date is before their start date (blocked scope, PQ-36); the nightly build (REQ-PIPELINE-080).
- **Design Traceability**: DESIGN.md Layout and Spacing; DESIGN.md Accessibility (WCAG 2.2 AA, visible focus, keyboard use, `lang`); `style-guide.html` as the rendered reference
- **Architecture Traceability**: Site Build (upcoming/current/past grouping in Europe/Berlin)
- **Security Traceability**: N/A

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Exhibitions/News page (both languages); grouping function reused by REQ-HOME-020 and REQ-CV-020
- **Actors**: Visitor (anonymous)
- **Preconditions**: REQ-EXHIBITIONS-010, REQ-CONTENT-040
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

1. **AC-01 — Expected behavior**: Given today 2026-06-10 in Europe/Berlin and entries 06-01–06-30, 07-01–07-31 and 2025-01-01–2025-02-01, when the page is generated, then they are current, upcoming and past.
2. **AC-02 — Boundary or failure behavior**: Given an entry starting today and one ending today, when the page is generated, then both are current.
3. **AC-03 — Boundary or failure behavior**: Given the build runs at 23:30 UTC on 2026-06-30 (01:30 on 07-01 in Berlin), when an entry ending 2026-06-30 is grouped, then it is past.
4. **AC-04 — Prohibited behavior**: Given past entries from many years, when the page is generated, then none is dropped from the archive.
5. **AC-05 — Additional criterion**: Given two current entries starting 05-01 and 06-01, when the page is generated, then 05-01 is listed first; given two past entries ending 03-01 and 04-01, 04-01 is listed first.

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

- **Unit Tests**: Grouping and ordering function with an injected date and time zone
- **Integration Tests**: Builds with fixed build timestamps around midnight Berlin time
- **Security Tests**: REQ-OUTPUT-010 hostile fixtures run against this page type; the REQ-PRIV-010 network test covers this page type
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:EXHIBITIONS-020/AC-01`; AC-02 → `test:EXHIBITIONS-020/AC-02`; AC-03 → `test:EXHIBITIONS-020/AC-03`; AC-04 → `test:EXHIBITIONS-020/AC-04`; AC-05 → `test:EXHIBITIONS-020/AC-05` (test command TO BE DECIDED by the first coding PR, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model; the last two major versions of Chrome, Edge, Firefox, Safari (macOS, iOS) and Samsung Internet (NFR-4); keyboard-only and screen reader check

## Dependencies

- **Upstream Requirements**: REQ-EXHIBITIONS-010 ({{ISSUE_URL:REQ-EXHIBITIONS-010}}), REQ-CONTENT-040 ({{ISSUE_URL:REQ-CONTENT-040}})
- **Downstream Requirements**: REQ-HOME-020 ({{ISSUE_URL:REQ-HOME-020}}), REQ-PERF-010 ({{ISSUE_URL:REQ-PERF-010}}), REQ-NEWS-010 ({{ISSUE_URL:REQ-NEWS-010}}), REQ-CV-020 ({{ISSUE_URL:REQ-CV-020}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A: no runtime dependency; a failed build leaves the previous site online

## Implementation Notes

- **Constraints**: Hugo (ARCHITECTURE.md Site Build); no client framework, and any first-party JavaScript follows SEC-OUT-5 (values inserted as text only); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Template text that editors are expected to change (DR-2); content rules evaluated in the visitor's browser (DR-3)
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; NOT VERIFIED, see ISSUE_PLAN.md Planning Rules), because: Date-boundary logic fully defined by FR-4.1, with clear test cases.
- **Required Human Review**: Product review by Nina (content and layout); design review against DESIGN.md and `style-guide.html`
- **Open Decisions**: PQ-36 (blocking for the excluded case only); PQ-46 (non-blocking): group headings.
