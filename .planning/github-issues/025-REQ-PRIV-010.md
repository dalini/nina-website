## Metadata

- **ID**: REQ-PRIV-010
- **Title**: Request no third-party resource before consent and include no analytics
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Repository maintainer (dalini); the maintainer role is still open in SECURITY.md SQ-10
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: Critical
- **Requirement Type**: Privacy
- **Source / Parent**: SECURITY.md SEC-PRIV-1, SEC-PRIV-5; REQUIREMENTS.md NFR-3; parent {{ISSUE_URL:REQ-EPIC-001}}

## Requirement

- **Statement**: No public page MUST request any resource from another origin before the visitor gives consent, all fonts and site assets MUST be self-hosted, and the site MUST NOT include analytics or tracking.
- **Rationale**: Requests to third parties transmit visitors' IP addresses without consent (GDPR; SECURITY.md T-34); analytics stay out unless REQUIREMENTS.md OQ-8 decides for them (SEC-PRIV-5).
- **Assumptions**: None
- **Out of Scope**: Consent-gated Instagram loading (SEC-PRIV-2, blocked by PQ-7); cookieless self-hosted analytics if OQ-8 decides for them (SEC-PRIV-5, SQ-5).
- **Design Traceability**: DESIGN.md Typography (system UI stack, no font files); DESIGN.md Accessibility (WCAG 2.2 AA, visible focus, keyboard use, `lang`); `style-guide.html` as the rendered reference
- **Architecture Traceability**: Public Site, Site Build templates; DR-6
- **Security Traceability**: SEC-PRIV-1, SEC-PRIV-5

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Every public page type in both languages
- **Actors**: Visitor (anonymous)
- **Preconditions**: REQ-NAV-020 (page types exist); each new page type adds itself to the test
- **Data Classification**: Public
- **Personal or Regulated Data**: Personal Data (visitor IP addresses would be sent to third parties if this failed)
- **Jurisdiction / Regulatory Scope**: Germany / EU: GDPR

## Security Context

- **Security Objectives**: Privacy
- **Control Layers**: Architecture, Data Protection
- **Threat References**: STRIDE Information Disclosure: SECURITY.md Threat Model v1 T-34
- **Abuse / Misuse Case**: A template or editor content references an external font, image, script or embed (T-34).
- **Trust Boundary**: TB-1 (visitor browser ↔ Static Hosting)
- **Untrusted Inputs or Assertions**: Editor content that may contain external URLs; templates
- **Authoritative Enforcement Point**: Site Build output, verified by an automated browser test in the pipeline
- **Independent Verification**: The browser test observes actual network requests rather than trusting template review
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: TO BE DECIDED: SECURITY.md cites ASVS 5.0.0 but uses no ASVS identifiers until they are verified; assurance level open in SECURITY.md SQ-9
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: TO BE DECIDED: not assessed
- **NIST SP 800-207**: N/A
- **Regulatory**: GDPR (Regulation (EU) 2016/679); specific articles TO BE DECIDED (not assessed in the source documents)
- **Other**: OWASP Top 10 Proactive Controls 2024 (`REF-PC-2024`)
- **Mapping Basis**: Other: OWASP Proactive Controls 2024 (`REF-PC-2024`) as cited in SECURITY.md; Regulatory: GDPR, article not assessed.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given every page type (Works, work page, public-space view, Texts, text page, CV, Contact, Impressum, Datenschutzerklärung, download links' page) in both languages, when each is loaded in a browser that records network requests, then every request goes to the site's own origin.
2. **AC-02 — Boundary or failure behavior**: Given a fixture work whose short text contains an image with an external URL, when the site is built and loaded, then no request goes to that URL (rejected or removed by REQ-OUTPUT-010/030) and the test reports any such request as failure.
3. **AC-03 — Prohibited behavior**: Given the generated output, when it is scanned, then it contains no analytics or tracking script, pixel or tag, and no reference to an external font.

## Failure Behavior

- **On Invalid Input**: A page that makes a third-party request fails the test and blocks deploy
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication is blocked scope (ISSUE_PLAN.md PQ-2)
- **On Authorization Failure**: N/A: all public pages are read-only and need no account (NFR-2)
- **On Security-Decision Failure**: An unrecognized origin counts as third party (deny by default)
- **On External Dependency Failure**: N/A
- **On System Error**: If the browser test cannot run, the pipeline does not deploy
- **Logging / Audit**: Test report lists page, language and requested origin
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: N/A: browser-level check
- **Integration Tests**: Browser test over every page type in both languages, run on every build in CI
- **Security Tests**: Hostile fixtures with external images, iframes and font references in content
- **Compliance Tests / Evidence**: Test report kept per release as evidence of no pre-consent third-party requests
- **Acceptance-Criteria Traceability**: AC-01 → `test:PRIV-010/AC-01`; AC-02 → `test:PRIV-010/AC-02`; AC-03 → `test:PRIV-010/AC-03`
- **Coverage Target**: Project coverage target TO BE DECIDED (build and test commands are TO BE DECIDED in CLAUDE.md). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Headless browser with request recording against the built site; Static site generator: TO BE DECIDED (PQ-16, ARCHITECTURE.md AQ-1)

## Dependencies

- **Upstream Requirements**: REQ-NAV-020 ({{ISSUE_URL:REQ-NAV-020}})
- **Downstream Requirements**: None
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A

## Implementation Notes

- **Constraints**: Static site generator: TO BE DECIDED (PQ-16, ARCHITECTURE.md AQ-1); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: CDN-hosted libraries or fonts; third-party analytics; allow-listing a third-party origin in the test
- **Implementation Guidance**: Run the test in REQ-PIPELINE-010's pipeline before deploy; extend its page-type list as blocked page types are unblocked.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`), because: Browser network-recording test over a known list of page types; the rule is unambiguous.
- **Required Human Review**: Privacy review
- **Open Decisions**: Non-blocking: PQ-16, PQ-21. OQ-8 (analytics) stays undecided; this issue implements the default of none (SEC-PRIV-5).
