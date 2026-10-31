## Metadata

- **ID**: REQ-PRIV-010
- **Title**: Request no third-party resource except the Google Analytics origins, and include no other analytics, tracking, cookies or pixels in any page
- **Version**: 0.3.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Privacy
- **Source / Parent**: SECURITY.md SEC-PRIV-1, SEC-PRIV-5 (no other analytics or tracking); REQUIREMENTS.md NFR-3, FR-8.3; DESIGN.md Typography (system fonts); parent {{ISSUE_URL:REQ-EPIC-001}}

## Requirement

- **Statement**: No page of the Public Site MUST request any third-party resource (fonts, scripts, images, embeds such as Instagram/Meta, video or maps) in the visitor's browser, before or after consent, other than the requests the Google tag makes to the Google Analytics origins of SEC-HTTP-2 (SEC-PRIV-5); fonts and all site assets MUST be self-hosted; and no page MUST include any analytics or tracking script, cookie or pixel other than Google Analytics under SEC-PRIV-5.
- **Rationale**: Visitors' IP addresses go to no third party except Google Analytics, which SEC-PRIV-5 governs (NFR-3); Instagram content is self-hosted, so it needs no consent step (FR-8.3).
- **Assumptions**: None
- **Out of Scope**: The Google tag, Consent Mode and the consent banner (REQ-CONSENT-010 to REQ-CONSENT-030); keeping Google Analytics off the preview and editing hosts (REQ-CONSENT-040).
- **Design Traceability**: N/A
- **Architecture Traceability**: Public Site (loads nothing from third parties except the Google Analytics tag with Consent Mode, which only the first-party consent script inserts); Site Build templates; DR-6
- **Security Traceability**: SEC-PRIV-1, SEC-PRIV-5 (no other analytics), SEC-HTTP-2 (allowed origins)

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Every page type in both languages
- **Actors**: Visitor (anonymous)
- **Preconditions**: REQ-LAYOUT-010, REQ-NAV-020, REQ-CONSENT-020
- **Data Classification**: Public
- **Personal or Regulated Data**: Personal Data (visitor IP addresses and browser data, sent only to Google Analytics under SEC-PRIV-5)
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Privacy
- **Control Layers**: Data Protection
- **Threat References**: STRIDE Information Disclosure: SECURITY.md Threat Model v3 T-34; CWE-359
- **Abuse / Misuse Case**: Editor content or a template references an external image or embed
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
- **Regulatory**: GDPR (Regulation (EU) 2016/679); no article mapping is kept (SECURITY.md)
- **Other**: OWASP Top 10 Proactive Controls 2024 (`REF-PC-2024`)
- **Mapping Basis**: Other: `REF-PC-2024` as cited in SECURITY.md; Regulatory: GDPR, article not assessed.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given the built site, when every page type is loaded in a browser with network recording before any choice, after Accept and after Decline, then every request goes to the site's own origin or to a Google Analytics origin listed in SEC-HTTP-2.
2. **AC-02 — Boundary or failure behavior**: Given fixture content that references an external image URL, when the site is built, then that reference is not in the output (REQ-OUTPUT-010) and the browser test still sees only own-origin requests.
3. **AC-03 — Prohibited behavior**: Given the build output, when it is searched, then it contains no analytics, tracking or advertising script, pixel or embed other than the first-party consent script that inserts the Google tag, and no font, script, style or image URL on another host.
4. **AC-04 — Additional criterion**: Given no choice or Decline, when any page is loaded, then the site's own responses set no cookie (the consent choice is stored in browser storage by REQ-CONSENT-010).

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
- **Integration Tests**: Automated browser test recording all requests on every page type
- **Security Tests**: Output scan for third-party URLs in `src`, `href` of resources, CSS `url()` and `srcset`; network recordings compared with the Google Analytics origins of SEC-HTTP-2
- **Compliance Tests / Evidence**: Evidence: network recordings of every page type, for the Datenschutzerklärung review
- **Acceptance-Criteria Traceability**: AC-01 → `test:PRIV-010/AC-01`; AC-02 → `test:PRIV-010/AC-02`; AC-03 → `test:PRIV-010/AC-03`; AC-04 → `test:PRIV-010/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model, with a test measurement ID; the last two major versions of Chrome, Edge, Firefox, Safari (macOS, iOS) and Samsung Internet (NFR-4); keyboard-only and screen reader check

## Dependencies

- **Upstream Requirements**: REQ-LAYOUT-010 ({{ISSUE_URL:REQ-LAYOUT-010}}), REQ-NAV-020 ({{ISSUE_URL:REQ-NAV-020}}), REQ-CONSENT-020 ({{ISSUE_URL:REQ-CONSENT-020}})
- **Downstream Requirements**: None
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A: no runtime dependency; a failed build leaves the previous site online

## Implementation Notes

- **Constraints**: Hugo (ARCHITECTURE.md Site Build); no client framework, and any first-party JavaScript follows SEC-OUT-5 (values inserted as text only); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Template text that editors are expected to change (DR-2); content rules evaluated in the visitor's browser (DR-3)
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: A browser network-recording test over a known list of page types; the rule is unambiguous.
- **Required Human Review**: Product review by Nina (content and layout); design review against DESIGN.md and `style-guide.html`
- **Open Decisions**: None
