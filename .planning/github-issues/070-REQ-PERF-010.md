## Metadata

- **ID**: REQ-PERF-010
- **Title**: Meet Core Web Vitals "good" and a 1.5 MB initial page weight on every page type in a Lighthouse mobile run
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: Medium
- **Requirement Type**: Performance
- **Source / Parent**: REQUIREMENTS.md NFR-1; parent https://github.com/dalini/nina-website/issues/2

## Requirement

- **Statement**: Every page type MUST meet, in a Lighthouse mobile run (mid-range phone, 4G): Largest Contentful Paint ≤ 2.5 s, Cumulative Layout Shift ≤ 0.1, Interaction to Next Paint ≤ 200 ms, and an initial page weight of at most 1.5 MB.
- **Rationale**: Most visitors open the site on phones (NFR-1).
- **Assumptions**: The measurements run with the consent banner shown and the Google tag as shipped by REQ-CONSENT-020, and with the image viewer script on work pages (REQ-WORKS-050)
- **Out of Scope**: Producing image sizes (REQ-IMAGES-010).
- **Design Traceability**: DESIGN.md Layout and Spacing (images keep their aspect ratio); DESIGN.md Accessibility (WCAG 2.2 AA, visible focus, keyboard use, `lang`); `style-guide.html` as the rendered reference
- **Architecture Traceability**: Site Build; Static Hosting; Public Site
- **Security Traceability**: N/A

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Home, Works, public-space group, work page, Exhibitions/News, Texts, Text page, CV, Contact, Impressum, Datenschutzerklärung (both languages)
- **Actors**: Visitor (anonymous)
- **Preconditions**: REQ-HOME-010, REQ-WORKS-010, REQ-WORKS-030, REQ-EXHIBITIONS-020, REQ-NEWS-010, REQ-TEXTS-010, REQ-CV-010, REQ-CONTACT-010, REQ-LEGAL-010, REQ-INSTAGRAM-020, REQ-IMAGES-010, REQ-WORKS-050, REQ-EXHIBITIONS-030, REQ-CONSENT-020
- **Data Classification**: Public
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Availability
- **Control Layers**: Other
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

1. **AC-01 — Expected behavior**: Given the built site with fixture content, when Lighthouse mobile runs on each page type, then LCP ≤ 2.5 s, CLS ≤ 0.1 and initial page weight ≤ 1.5 MB on every one.
2. **AC-02 — Expected behavior**: Given a scripted interaction on each page type (e.g. the language switch), when INP is measured in the Lighthouse run, then it is ≤ 200 ms.
3. **AC-03 — Boundary or failure behavior**: Given a change that pushes one page type over a limit, when the check runs in CI, then it fails and names the page type and metric.
4. **AC-04 — Prohibited behavior**: Given any page, when it loads, then no image is shown at a size larger than its rendered box needs.

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

- **Unit Tests**: N/A
- **Integration Tests**: Lighthouse mobile run in CI against the built site served locally
- **Security Tests**: N/A
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:PERF-010/AC-01`; AC-02 → `test:PERF-010/AC-02`; AC-03 → `test:PERF-010/AC-03`; AC-04 → `test:PERF-010/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model with large fixture images; Lighthouse mobile preset (mid-range phone, 4G)

## Dependencies

- **Upstream Requirements**: REQ-HOME-010 (https://github.com/dalini/nina-website/issues/38), REQ-WORKS-010 (https://github.com/dalini/nina-website/issues/39), REQ-WORKS-030 (https://github.com/dalini/nina-website/issues/40), REQ-EXHIBITIONS-020 (https://github.com/dalini/nina-website/issues/49), REQ-NEWS-010 (https://github.com/dalini/nina-website/issues/51), REQ-TEXTS-010 (https://github.com/dalini/nina-website/issues/55), REQ-CV-010 (https://github.com/dalini/nina-website/issues/52), REQ-CONTACT-010 (https://github.com/dalini/nina-website/issues/57), REQ-LEGAL-010 (https://github.com/dalini/nina-website/issues/58), REQ-INSTAGRAM-020 (https://github.com/dalini/nina-website/issues/60), REQ-IMAGES-010 (https://github.com/dalini/nina-website/issues/37), REQ-WORKS-050 (https://github.com/dalini/nina-website/issues/41), REQ-EXHIBITIONS-030 (https://github.com/dalini/nina-website/issues/47), REQ-CONSENT-020 (https://github.com/dalini/nina-website/issues/70)
- **Downstream Requirements**: None
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A: no runtime dependency; a failed build leaves the previous site online

## Implementation Notes

- **Constraints**: Hugo (ARCHITECTURE.md Site Build); no client framework, and any first-party JavaScript follows SEC-OUT-5 (values inserted as text only); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Template text that editors are expected to change (DR-2); content rules evaluated in the visitor's browser (DR-3)
- **Implementation Guidance**: Lab Lighthouse measures INP only with a user-flow or timespan run; script one interaction per page type.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Measurement and tuning against fixed numeric budgets.
- **Required Human Review**: Product review by Nina (content and layout); design review against DESIGN.md and `style-guide.html`
- **Open Decisions**: None
