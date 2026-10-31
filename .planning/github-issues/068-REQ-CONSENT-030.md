## Metadata

- **ID**: REQ-CONSENT-030
- **Title**: Set Google Analytics cookies for the host nina-laaf.de only, so that edit. and preview. never receive them
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Privacy
- **Source / Parent**: SECURITY.md SEC-PRIV-5 (cookies for the host `nina-laaf.de` only), `REF-GTAG-COOKIES`; T-79; parent {{ISSUE_URL:REQ-CONSENT-000}}

## Requirement

- **Statement**: Google Analytics cookies MUST be set for the host `nina-laaf.de` only, instead of Google's automatic highest-level cookie domain, so that browsers never send them to `edit.nina-laaf.de` or `preview.nina-laaf.de`.
- **Rationale**: By default the tag sets its cookies on `.nina-laaf.de`, which would send the visitor's identifiers to the editing and preview hosts and expose them to script there (T-79).
- **Assumptions**: None
- **Out of Scope**: Consent Mode and tag insertion (REQ-CONSENT-020).
- **Design Traceability**: N/A
- **Architecture Traceability**: Public Site (Google Analytics cookies after consent); DR-9 (separate origins, one site for cookies)
- **Security Traceability**: SEC-PRIV-5 (cookie domain)

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Google tag cookie configuration
- **Actors**: Visitor; Google (Google tag)
- **Preconditions**: REQ-CONSENT-020
- **Data Classification**: Internal
- **Personal or Regulated Data**: Personal Data (Google Analytics identifiers in cookies)
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Privacy, Confidentiality
- **Control Layers**: Data Protection
- **Threat References**: STRIDE Information Disclosure: SECURITY.md Threat Model v3 T-79; CWE-200
- **Abuse / Misuse Case**: Script on `edit.` or `preview.` reads the visitor's Google Analytics identifiers from cookies scoped to the parent domain
- **Trust Boundary**: TB-2 and TB-6 (cookies shared across the hosts of one site)
- **Untrusted Inputs or Assertions**: The Google tag's own cookie-domain behavior
- **Authoritative Enforcement Point**: The tag configuration in the first-party consent script; the browser's cookie scoping (RFC 6265)
- **Independent Verification**: Browser test reading the cookie jar and the request headers on all three hosts
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: Google tag platform: configure and customize cookies (`REF-GTAG-COOKIES`); IETF RFC 6265 HTTP State Management Mechanism, sections 4.1.2.3 and 5.3 (Domain attribute and host-only cookies)
- **Mapping Basis**: Other: `REF-GTAG-COOKIES` as cited in SECURITY.md SEC-PRIV-5; RFC 6265 defines when a cookie is sent to subdomains.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given the visitor Accepts on `nina-laaf.de`, when the Google Analytics cookies are set, then each is a host-only cookie of `nina-laaf.de` (no `Domain` attribute naming a parent domain).
2. **AC-02 — Boundary or failure behavior**: Given those cookies exist, when the browser requests `https://edit.nina-laaf.de/` and `https://preview.nina-laaf.de/`, then neither request carries a Google Analytics cookie, and `document.cookie` on those hosts shows none.
3. **AC-03 — Prohibited behavior**: Given the tag configuration, when it is reviewed, then it does not use Google's automatic cookie domain, and no Google cookie is set for `.nina-laaf.de`.

## Failure Behavior

- **On Invalid Input**: N/A: no input is processed; the setting is fixed in the consent script
- **On Authentication Failure**: N/A: public pages need no sign-in (NFR-2)
- **On Authorization Failure**: N/A: all public pages are read-only and need no account (NFR-2)
- **On Security-Decision Failure**: If no tag setting yields host-only cookies, the issue records that in SEC-PRIV-5 and ships cookies for `nina-laaf.de` as the accepted risk decided in step 10 a third time (PQ-61); `edit.` and `preview.` neither read nor log them
- **On External Dependency Failure**: N/A
- **On System Error**: N/A
- **Logging / Audit**: N/A
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: N/A: one configuration value
- **Integration Tests**: Browser test across `nina-laaf.de`, `edit.nina-laaf.de` and `preview.nina-laaf.de` test hosts after Accept
- **Security Tests**: Inspection of `Set-Cookie`-equivalent attributes written by the tag; request headers to the other hosts
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:CONSENT-030/AC-01`; AC-02 → `test:CONSENT-030/AC-02`; AC-03 → `test:CONSENT-030/AC-03` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Three test hosts under one registrable domain with TLS, mirroring `nina-laaf.de`, `edit.` and `preview.`; the last two major versions of Chrome, Edge, Firefox, Safari (macOS, iOS) and Samsung Internet (NFR-4)

## Dependencies

- **Upstream Requirements**: REQ-CONSENT-020 ({{ISSUE_URL:REQ-CONSENT-020}})
- **Downstream Requirements**: None
- **External Dependencies**: Google tag (gtag.js)
- **Dependency Assumptions**: The tag's `cookie_domain` setting controls the `Domain` attribute it writes (`REF-GTAG-COOKIES`); whether a value of `nina-laaf.de` or `none` yields a host-only cookie is verified at implementation (PQ-61, step 10 a third time)
- **Failure Impact**: A parent-domain cookie leaks identifiers to the other hosts (T-79)

## Implementation Notes

- **Constraints**: Configuration in the first-party consent script (REQ-CONSENT-020); no inline script
- **Prohibited Approaches**: Google's automatic cookie domain; a `Domain=nina-laaf.de` cookie, which RFC 6265 sends to every subdomain
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: The setting must be verified against how browsers scope cookies (RFC 6265), and the issue must stop rather than ship a mechanism that does not meet SEC-PRIV-5.
- **Required Human Review**: Privacy review by Ives
- **Open Decisions**: None. Answered in step 10 a third time (`.planning/OPEN_QUESTIONS.md`): PQ-61: the `cookie_domain` value is verified at implementation; if none gives host-only cookies, cookies for `nina-laaf.de` are an accepted risk (SEC-PRIV-5).
