## Metadata

- **ID**: REQ-TEXTS-010
- **Title**: List published texts and give each its own page
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: Medium
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-5.1; parent {{ISSUE_URL:REQ-EPIC-001}}

## Requirement

- **Statement**: The Texts page MUST list every published essay and catalogue text, each linking to its own page that shows the text's title and body.
- **Rationale**: Essays and catalogue texts are part of how the work is presented (FR-5.1).
- **Assumptions**: None
- **Out of Scope**: Ordering of the list (FR-5.1: Nina's manual order; optional year shown when entered); required fields of a Text (FR-10.6: title and body); images inside Text bodies (FR-5.1); empty language values (FR-1.7). Decided in step 10 and decomposed in the step 9 re-run.
- **Design Traceability**: DESIGN.md Typography (text width at most 68 characters, line height 1.6); Components (Links); DESIGN.md Accessibility (WCAG 2.2 AA, visible focus, keyboard use, `lang`); `style-guide.html` as the rendered reference
- **Architecture Traceability**: Content Store (Text), Site Build
- **Security Traceability**: SEC-OUT-1 (via REQ-OUTPUT-010)

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Texts page and text pages (DE, EN)
- **Actors**: Visitor (anonymous)
- **Preconditions**: REQ-CONTENT-020, REQ-CONTENT-040, REQ-NAV-020, REQ-OUTPUT-010
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

1. **AC-01 — Expected behavior**: Given two published texts, when the Site Build runs, then the Texts page lists both, and each link opens a page showing that text's title and body in the page's language.
2. **AC-02 — Boundary or failure behavior**: Given no published text, when the Site Build runs, then the Texts page renders without errors and lists none.
3. **AC-03 — Prohibited behavior**: Given an unpublished text, when the site is generated, then it is neither listed nor reachable (SEC-INPUT-4).

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

- **Unit Tests**: Text list selection; text page rendering
- **Integration Tests**: Full build with fixture texts in both languages
- **Security Tests**: Covered by REQ-OUTPUT-010 fixtures run against this page type; REQ-PRIV-010 network test includes this page type
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:TEXTS-010/AC-01`; AC-02 → `test:TEXTS-010/AC-02`; AC-03 → `test:TEXTS-010/AC-03`
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build run locally against fixture content in the documented content model; Static site generator: Hugo (ARCHITECTURE.md Site Build); the last two major versions of Chrome, Edge, Firefox, Safari (macOS, iOS) and Samsung Internet (NFR-4); screen reader check

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-020 ({{ISSUE_URL:REQ-CONTENT-020}}), REQ-CONTENT-040 ({{ISSUE_URL:REQ-CONTENT-040}}), REQ-NAV-020 ({{ISSUE_URL:REQ-NAV-020}}), REQ-OUTPUT-010 ({{ISSUE_URL:REQ-OUTPUT-010}})
- **Downstream Requirements**: None
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A

## Implementation Notes

- **Constraints**: Static site generator: Hugo (ARCHITECTURE.md Site Build); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Rendering text bodies as raw HTML (SEC-OUT-1)
- **Implementation Guidance**: Body text uses the restricted rich-text rendering from REQ-OUTPUT-010.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`), because: Routine list and detail pages.
- **Required Human Review**: Product review by Nina (content and layout); design review against DESIGN.md and `style-guide.html`
- **Open Decisions**: None (step 10: order FR-5.1; rich text SEC-OUT-1; browsers NFR-4).
