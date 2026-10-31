## Metadata

- **ID**: REQ-INSTAGRAM-010
- **Title**: Link to Instagram account @ninalaaf without loading any Instagram content
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: Medium
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-8.1, FR-8.4 (link part); SECURITY.md SEC-PRIV-1; parent {{ISSUE_URL:REQ-EPIC-001}}

## Requirement

- **Statement**: The site MUST show a link to the Instagram account @ninalaaf, and showing that link MUST NOT request any resource from Instagram or Meta.
- **Rationale**: Visitors find Nina's Instagram (FR-8.1); the link is what remains when the feed cannot be shown (FR-8.4).
- **Assumptions**: None
- **Out of Scope**: The posts on the home page, their refresh, failure and hide behavior (FR-8.2 to FR-8.6, SEC-PRIV-3, SEC-OUT-3, SEC-SECRET-3): build-time model decided in step 10 and decomposed in the step 9 re-run.
- **Design Traceability**: DESIGN.md Components (Links); DESIGN.md Accessibility (WCAG 2.2 AA, visible focus, keyboard use, `lang`); `style-guide.html` as the rendered reference
- **Architecture Traceability**: Site Build, Public Site; DR-5 (no Instagram data outside the Instagram Feed Integration)
- **Security Traceability**: SEC-PRIV-1, SEC-OUT-2

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Instagram link in the footer of every page (FR-8.1)
- **Actors**: Visitor (anonymous)
- **Preconditions**: REQ-NAV-020, REQ-OUTPUT-030
- **Data Classification**: Public
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Privacy
- **Control Layers**: Architecture, Output Encoding
- **Threat References**: STRIDE Information Disclosure: SECURITY.md Threat Model v1 T-34
- **Abuse / Misuse Case**: An embedded Instagram icon or widget sends visitor data to Meta before consent (T-34).
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

1. **AC-01 — Expected behavior**: Given the generated site, when any page is loaded, then its footer shows a text link to the @ninalaaf account over `https:` with `rel="noopener"`.
2. **AC-02 — Boundary or failure behavior**: Given those pages are loaded in a browser with network recording, when loading completes, then no request goes to an Instagram or Meta origin.
3. **AC-03 — Prohibited behavior**: Given the link, when it is inspected, then it uses no Instagram-hosted image, script or font.

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

- **Unit Tests**: N/A: template link
- **Integration Tests**: Full build: link present; network recording shows no Instagram/Meta request
- **Security Tests**: Network recording as in REQ-PRIV-010 on the pages with the link
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:INSTAGRAM-010/AC-01`; AC-02 → `test:INSTAGRAM-010/AC-02`; AC-03 → `test:INSTAGRAM-010/AC-03`
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build run locally against fixture content in the documented content model; Static site generator: Hugo (ARCHITECTURE.md Site Build); the last two major versions of Chrome, Edge, Firefox, Safari (macOS, iOS) and Samsung Internet (NFR-4); screen reader check

## Dependencies

- **Upstream Requirements**: REQ-NAV-020 ({{ISSUE_URL:REQ-NAV-020}}), REQ-OUTPUT-030 ({{ISSUE_URL:REQ-OUTPUT-030}})
- **Downstream Requirements**: None
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A

## Implementation Notes

- **Constraints**: Static site generator: Hugo (ARCHITECTURE.md Site Build); DEP-1 to DEP-8 apply to every dependency added; self-hosted icon if any (SEC-PRIV-1)
- **Prohibited Approaches**: Instagram embed scripts or hot-linked Instagram images
- **Implementation Guidance**: Whether the link is template text or Page content is not specified; the account name is fixed by FR-8.1.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`), because: A plain link plus a network check; no feed logic in scope.
- **Required Human Review**: Product review by Nina (content and layout); design review against DESIGN.md and `style-guide.html`
- **Open Decisions**: None (step 10: footer of every page; browsers NFR-4).
