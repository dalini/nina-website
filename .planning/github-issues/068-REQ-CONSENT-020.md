## Metadata

- **ID**: REQ-CONSENT-020
- **Title**: Set Consent Mode defaults first, insert the Google tag only from the consent script, and update consent only for analytics storage
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 fourth run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Privacy
- **Source / Parent**: SECURITY.md SEC-PRIV-5 (Consent Mode v2 advanced, tag inserted only by the consent script, only the property's measurement ID, ad consent types always denied), SEC-HTTP-2 (`script-src` limited to `https://www.googletagmanager.com`, step 10 a third time), SEC-PRIV-7 (no Tag Manager), `REF-GTAG-CONSENT`, `REF-GTAG-CSP`; REQUIREMENTS.md FR-9.2, NFR-3; T-69, T-71, T-72, T-76; parent https://github.com/dalini/nina-website/issues/11

## Requirement

- **Statement**: The first-party consent script MUST set `analytics_storage`, `ad_storage`, `ad_user_data` and `ad_personalization` to denied before the Google tag loads, and only then insert `gtag.js` for the property's own GA4 measurement ID from `https://www.googletagmanager.com` (no Google Tag Manager container, no other destination); no generated page may contain the tag itself, so the tag does not load when that script does not run; Accept MUST grant `analytics_storage` only, Decline or withdrawal MUST set it back to denied, and the three ad consent types MUST stay denied in every state; the tag MUST work under the SEC-HTTP-2 `script-src` source `https://www.googletagmanager.com`, which may be widened only if a browser test shows the tag needs more, recorded in SEC-HTTP-2 in the same pull request.
- **Rationale**: Advanced Consent Mode sends only cookieless pings before consent (SEC-PRIV-5); inserting the tag from the consent script makes the defaults come first (T-71) and keeps an injected tag from being the only one (T-76).
- **Assumptions**: The property exists with its measurement ID (REQ-PRIV-030)
- **Out of Scope**: The banner and the stored choice (REQ-CONSENT-010); the banner version (REQ-CONSENT-050); the cookie domain (REQ-CONSENT-030); preview and editing hosts (REQ-CONSENT-040); the CSP header itself (REQ-HTTP-020).
- **Design Traceability**: N/A
- **Architecture Traceability**: Public Site (the Google tag with Consent Mode, inserted only by the first-party consent script, T-71); DR-6
- **Security Traceability**: SEC-PRIV-5, SEC-HTTP-2, SEC-OUT-7

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Consent defaults, tag insertion and consent updates in the first-party consent script; requests to the Google Analytics origins
- **Actors**: Visitor; Google (Google tag, measurements)
- **Preconditions**: REQ-CONSENT-010, REQ-PRIV-030, REQ-HTTP-020, REQ-OUTPUT-040
- **Data Classification**: Internal
- **Personal or Regulated Data**: Personal Data (IP address and browser data sent to Google, also in cookieless pings; accepted risk, SEC-PRIV-5)
- **Jurisdiction / Regulatory Scope**: Germany / EU: GDPR; consent for statistics; no article mapping (SECURITY.md)

## Security Context

- **Security Objectives**: Privacy
- **Control Layers**: Business-Rule Validation, Supply Chain
- **Threat References**: STRIDE Information Disclosure and Elevation of Privilege: SECURITY.md Threat Model v3 T-69, T-71, T-72, T-74 (accepted), T-76; CWE-359
- **Abuse / Misuse Case**: A tag that starts before the defaults, or a tag of another measurement ID or container, collects more than the visitor agreed to
- **Trust Boundary**: TB-6 (visitor browser ↔ Google Analytics, DF-14)
- **Untrusted Inputs or Assertions**: The Google tag served live by Google (accepted risk, SEC-PRIV-5); the stored choice (read through REQ-CONSENT-010)
- **Authoritative Enforcement Point**: The first-party consent script (order of defaults and tag insertion); the build check of REQ-OUTPUT-040 (no tag in generated pages)
- **Independent Verification**: Network-level browser tests of cookies and requests in each consent state
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: Google tag platform: consent mode overview (`REF-GTAG-CONSENT`), CSP guide (`REF-GTAG-CSP`)
- **Mapping Basis**: Other: `REF-GTAG-CONSENT` and `REF-GTAG-CSP` as cited in SECURITY.md SEC-PRIV-5 and SEC-HTTP-2.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given no choice yet, when a page loads, then the consent script sets `analytics_storage`, `ad_storage`, `ad_user_data` and `ad_personalization` to denied, then inserts the Google tag for the property's GA4 measurement ID, no Google cookie is set, and only cookieless requests reach the Google origins.
2. **AC-02 — Boundary or failure behavior**: Given the visitor Accepts, when the consent update runs, then `analytics_storage` is granted, Google Analytics cookies are set, and the consent state sent to Google grants no ad consent type; given Decline or a later withdrawal, `analytics_storage` is denied again and no Google cookie is set again.
3. **AC-03 — Boundary or failure behavior**: Given the consent script is blocked or throws before setting the defaults, when the page loads, then no request reaches any Google origin.
4. **AC-04 — Prohibited behavior**: Given the production output and the tag configuration, when they are reviewed, then no page contains the tag loader, no Google Tag Manager container ID and no measurement ID other than the property's own, and no other destination is configured.
5. **AC-05 — Additional criterion**: Given the CSP of REQ-HTTP-020 with `script-src 'self' https://www.googletagmanager.com`, when the SEC-PRIV-5 browser tests run before consent, after Accept and after Decline, then the tag loads and sends its requests without a CSP violation; if a test shows the tag needs another script source, the pull request that widens `script-src` also records the wider source in SECURITY.md SEC-HTTP-2.

## Failure Behavior

- **On Invalid Input**: An unknown stored value counts as no choice (REQ-CONSENT-010); consent stays denied
- **On Authentication Failure**: N/A: public pages need no sign-in (NFR-2)
- **On Authorization Failure**: N/A: all public pages are read-only and need no account (NFR-2)
- **On Security-Decision Failure**: N/A: this page makes no security decision; encoding and link rules are enforced by REQ-OUTPUT-010 and REQ-OUTPUT-030
- **On External Dependency Failure**: Google tag blocked or unreachable: nothing is inserted or sent; the page works (FR-9.2, T-84)
- **On System Error**: If the consent script fails before the defaults are set, it inserts no tag (T-71)
- **Logging / Audit**: N/A: no server-side component
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: Order of calls: defaults before insertion; consent update per choice; ad types denied in every state
- **Integration Tests**: Browser tests recording cookies and requests before consent, after Accept, after Decline and after withdrawal, with a test measurement ID
- **Security Tests**: Consent script blocked; injected second tag fixture rejected by REQ-OUTPUT-040; `script-src` narrowing tests
- **Compliance Tests / Evidence**: Browser-test record of the four consent states, kept with the pull request
- **Acceptance-Criteria Traceability**: AC-01 → `test:CONSENT-020/AC-01`; AC-02 → `test:CONSENT-020/AC-02`; AC-03 → `test:CONSENT-020/AC-03`; AC-04 → `test:CONSENT-020/AC-04`; AC-05 → `test:CONSENT-020/AC-05` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model; the last two major versions of Chrome, Edge, Firefox, Safari (macOS, iOS) and Samsung Internet (NFR-4); keyboard-only and screen reader check

## Dependencies

- **Upstream Requirements**: REQ-CONSENT-010 (https://github.com/dalini/nina-website/issues/61), REQ-PRIV-030 (https://github.com/dalini/nina-website/issues/62), REQ-HTTP-020 (https://github.com/dalini/nina-website/issues/69), REQ-OUTPUT-040 (https://github.com/dalini/nina-website/issues/30)
- **Downstream Requirements**: REQ-CONSENT-050 (https://github.com/dalini/nina-website/issues/71), REQ-PERF-010 (https://github.com/dalini/nina-website/issues/72), REQ-PRIV-010 (https://github.com/dalini/nina-website/issues/73), REQ-CONSENT-030 (https://github.com/dalini/nina-website/issues/74), REQ-CONSENT-040 (https://github.com/dalini/nina-website/issues/104)
- **External Dependencies**: Google tag (gtag.js) and Google Analytics 4, served live by Google
- **Dependency Assumptions**: Google honours Consent Mode as documented (`REF-GTAG-CONSENT`); ASSUMPTION (SECURITY.md T-71): a tag started without defaults may treat consent as granted
- **Failure Impact**: Google unreachable: no statistics; a changed Google script can affect every page (accepted risk T-69)

## Implementation Notes

- **Constraints**: First-party script file only, no inline script (SEC-HTTP-2, SEC-OUT-7); no Google Tag Manager; the measurement ID in `dalini/nina-site` configuration; DEP-1 to DEP-8 apply
- **Prohibited Approaches**: A `<script>` element for the tag in any template; granting any ad consent type; a broader `script-src` than the narrowest working source
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Consent Mode depends on the order of first-party and Google code; a mistake sets cookies or sends full measurements before consent (T-71) and has to be proven by network-level browser tests.
- **Required Human Review**: Privacy and security review by Ives
- **Open Decisions**: None. Decided in step 10 a third time (`.planning/OPEN_QUESTIONS.md`): `script-src` is limited to `https://www.googletagmanager.com` (SEC-HTTP-2); no Google Tag Manager container or linked Tag Manager or Ads product (SEC-PRIV-7).
