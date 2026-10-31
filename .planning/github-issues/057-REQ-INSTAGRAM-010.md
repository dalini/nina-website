## Metadata

- **ID**: REQ-INSTAGRAM-010
- **Title**: Link to Instagram account @ninalaaf in the footer of every page without loading anything from Instagram
- **Version**: 0.3.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-8.1, FR-8.3; SECURITY.md SEC-PRIV-1, SEC-OUT-2; parent https://github.com/dalini/nina-website/issues/10

## Requirement

- **Statement**: The footer of every page MUST link to Instagram account @ninalaaf with an `https:` link carrying `rel="noopener"`, and the page MUST NOT request anything from Instagram or Meta.
- **Rationale**: Visitors can follow Nina on Instagram (FR-8.1) without their browser contacting Instagram (SEC-PRIV-1).
- **Assumptions**: None
- **Out of Scope**: The home page posts (REQ-INSTAGRAM-020).
- **Design Traceability**: DESIGN.md Components (Links); DESIGN.md Accessibility (WCAG 2.2 AA, visible focus, keyboard use, `lang`); `style-guide.html` as the rendered reference
- **Architecture Traceability**: Site Build; Public Site
- **Security Traceability**: SEC-PRIV-1, SEC-OUT-2

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Footer of every page
- **Actors**: Visitor (anonymous)
- **Preconditions**: REQ-LAYOUT-010, REQ-NAV-020, REQ-OUTPUT-030
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

1. **AC-01 — Expected behavior**: Given any page in either language, when it loads, then its footer links to the @ninalaaf Instagram profile over `https:` with `noopener`.
2. **AC-02 — Boundary or failure behavior**: Given the feed is switched off or no posts were ever fetched, when any page is built, then its footer still links to @ninalaaf (FR-8.4).
3. **AC-03 — Prohibited behavior**: Given any page, when its network requests are recorded, then none goes to an Instagram or Meta host, and no Instagram icon font, script or image is loaded from them.

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

- **Unit Tests**: N/A: template; covered by integration tests
- **Integration Tests**: Crawl: every page has the link
- **Security Tests**: REQ-PRIV-010 network recording on every page type
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:INSTAGRAM-010/AC-01`; AC-02 → `test:INSTAGRAM-010/AC-02`; AC-03 → `test:INSTAGRAM-010/AC-03` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model; the last two major versions of Chrome, Edge, Firefox, Safari (macOS, iOS) and Samsung Internet (NFR-4); keyboard-only and screen reader check

## Dependencies

- **Upstream Requirements**: REQ-LAYOUT-010 (https://github.com/dalini/nina-website/issues/31), REQ-NAV-020 (https://github.com/dalini/nina-website/issues/32), REQ-OUTPUT-030 (https://github.com/dalini/nina-website/issues/29)
- **Downstream Requirements**: REQ-INSTAGRAM-020 (https://github.com/dalini/nina-website/issues/60)
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A: no runtime dependency; a failed build leaves the previous site online

## Implementation Notes

- **Constraints**: Hugo (ARCHITECTURE.md Site Build); no client framework, and any first-party JavaScript follows SEC-OUT-5 (values inserted as text only); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Template text that editors are expected to change (DR-2); content rules evaluated in the visitor's browser (DR-3)
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: A plain link plus a network check.
- **Required Human Review**: Product review by Nina (content and layout); design review against DESIGN.md and `style-guide.html`
- **Open Decisions**: None
