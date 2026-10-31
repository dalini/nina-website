## Metadata

- **ID**: REQ-CONSENT-010
- **Title**: Show the consent banner with its fixed text until a visitor chooses, store the choice strictly, and let the footer link reopen it
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 fourth run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Privacy
- **Source / Parent**: REQUIREMENTS.md FR-9.2; DESIGN.md Components (Consent banner, Buttons, Focus state), Accessibility; SECURITY.md SEC-PRIV-5 (only an exact Accept value counts as consent), SEC-OUT-5; T-72, T-84; parent {{ISSUE_URL:REQ-CONSENT-000}}

## Requirement

- **Statement**: Until a visitor has chosen, every page of the public site `nina-laaf.de` MUST show the consent banner for visitor statistics with Accept and Decline buttons of equal prominence; its German and English text, button labels and footer link label MUST be fixed in the templates (not editable content) and MUST NOT state that nothing reaches Google before consent; the choice MUST be stored in the visitor's browser and MUST NOT be asked again until it is withdrawn; the footer of every page of the public site MUST offer a link that reopens the banner so the choice can be changed or withdrawn; only a stored value that exactly matches Accept MUST count as consent, and a missing, unknown or malformed value MUST count as no choice; pages MUST remain fully usable while the banner is shown and after Decline, also when the Google tag cannot load.
- **Rationale**: Google Analytics cookies are set only after the visitor accepts (SEC-PRIV-5); a strict reading of the stored value keeps consent from being assumed (T-72).
- **Assumptions**: Ives writes the banner text (DE and EN text, Accept and Decline labels, footer link) together with the Datenschutzerklärung at implementation, and Nina approves it (SECURITY.md Required Security Inputs; step 10 a third time, PQ-60)
- **Out of Scope**: The banner version stored with the choice and asking again when it changes (REQ-CONSENT-050); setting Consent Mode and inserting the Google tag (REQ-CONSENT-020); leaving the banner and its footer link out of the preview build (REQ-CONSENT-040); the Datenschutzerklärung text (REQ-LEGAL-010).
- **Design Traceability**: DESIGN.md Components (Consent banner: bar fixed to the bottom on `surface`, short text, link to the Datenschutzerklärung, two Buttons of equal size and style, never covers the main content on load, does not block scrolling, footer link reopens it; Buttons; Focus state; Inputs and form feedback); Accessibility; `style-guide.html` as the rendered reference
- **Architecture Traceability**: Public Site (consent banner, first-party vanilla JavaScript; the visitor's consent choice in browser storage)
- **Security Traceability**: SEC-PRIV-5 (stored value), SEC-OUT-5; SEC-HTTP-2 and SEC-OUT-7 (own script file, no inline script)

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: Consent banner on every page; footer link that reopens it; browser storage entry for the choice
- **Actors**: Visitor (anonymous)
- **Preconditions**: REQ-LAYOUT-010, REQ-NAV-020, REQ-LEGAL-010
- **Data Classification**: Internal
- **Personal or Regulated Data**: Personal Data (the visitor's consent choice in browser storage)
- **Jurisdiction / Regulatory Scope**: Germany / EU: GDPR and consent for statistics; banner text approved by Nina with the Datenschutzerklärung; no article mapping (SECURITY.md)

## Security Context

- **Security Objectives**: Privacy, Integrity
- **Control Layers**: Business-Rule Validation, Output Encoding
- **Threat References**: STRIDE Information Disclosure, Tampering and Denial of Service: SECURITY.md Threat Model v3 T-72, T-32, T-84; CWE-79
- **Abuse / Misuse Case**: Other script in the origin, or an old banner version, writes a value that the banner would read as consent
- **Trust Boundary**: TB-1 (static pages served to visitors)
- **Untrusted Inputs or Assertions**: The stored consent value; values from the URL; the Google tag's presence
- **Authoritative Enforcement Point**: The first-party consent script on each page (strict parsing of the stored value)
- **Independent Verification**: Browser tests with each malformed stored value and with the Google tag blocked
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: W3C WCAG 2.2, Level AA (DESIGN.md Accessibility target); OWASP XSS Prevention Cheat Sheet (`REF-XSS`)
- **Mapping Basis**: Other: WCAG 2.2 Level AA as targeted by DESIGN.md; `REF-XSS` as cited in SECURITY.md SEC-OUT-5.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given a first visit with empty browser storage, when any page loads, then the banner is shown at the bottom of the viewport on `surface` with its text, a link to the Datenschutzerklärung and Accept and Decline buttons of equal size and style, and the page can be scrolled and used while it is shown.
2. **AC-02 — Boundary or failure behavior**: Given the visitor chose Accept or Decline, when she opens other pages and returns later, then the banner is not shown again; when she follows the footer link, the banner opens and a new choice replaces the old one.
3. **AC-03 — Boundary or failure behavior**: Given the stored value is missing, empty, an unknown string, a value of another format, or `<img src=x onerror=alert(1)>`, when a page loads, then the banner is shown, the value is treated as no choice, and no markup from it is rendered.
4. **AC-04 — Prohibited behavior**: Given the Google tag is blocked by an extension or Google is unreachable, when the visitor uses the page, Accepts, Declines or reopens the banner, then the page and the banner keep working and no script error stops them (T-84).
5. **AC-05 — Prohibited behavior**: Given the templates and the content model, when they are reviewed, then the banner text and labels exist in German and English only in the templates, no editor can change them through the Editing Interface, and neither language states or implies that no data reaches Google before consent.
6. **AC-06 — Additional criterion**: Given a keyboard-only user, when the banner is shown, then both buttons and the Datenschutzerklärung link are reachable with Tab, show the focus outline, and the banner does not cover the page's main content on load.

## Failure Behavior

- **On Invalid Input**: A stored value other than an exact Accept or Decline counts as no choice: the banner is shown and consent stays denied
- **On Authentication Failure**: N/A: public pages need no sign-in (NFR-2)
- **On Authorization Failure**: N/A: all public pages are read-only and need no account (NFR-2)
- **On Security-Decision Failure**: N/A: this page makes no security decision; encoding and link rules are enforced by REQ-OUTPUT-010 and REQ-OUTPUT-030
- **On External Dependency Failure**: Google tag blocked or unreachable: the banner and the page keep working (FR-9.2, T-84)
- **On System Error**: If the consent script fails, no consent is assumed and the Google tag is not inserted (REQ-CONSENT-020); the page stays usable
- **Logging / Audit**: N/A: the choice exists only in the visitor's browser; no record of it is kept outside the browser (FR-9.2)
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: Parsing of the stored value (exact Accept, exact Decline, everything else as no choice); storage unavailable or throwing
- **Integration Tests**: Browser tests on built pages in both languages: first visit, Accept, Decline, reopen from the footer, change of choice
- **Security Tests**: Hostile stored values and URL fragments (SEC-OUT-5); Google tag blocked by request interception
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:CONSENT-010/AC-01`; AC-02 → `test:CONSENT-010/AC-02`; AC-03 → `test:CONSENT-010/AC-03`; AC-04 → `test:CONSENT-010/AC-04`; AC-05 → `test:CONSENT-010/AC-05`; AC-06 → `test:CONSENT-010/AC-06` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model; the last two major versions of Chrome, Edge, Firefox, Safari (macOS, iOS) and Samsung Internet (NFR-4); keyboard-only and screen reader check

## Dependencies

- **Upstream Requirements**: REQ-LAYOUT-010 ({{ISSUE_URL:REQ-LAYOUT-010}}), REQ-NAV-020 ({{ISSUE_URL:REQ-NAV-020}}), REQ-LEGAL-010 ({{ISSUE_URL:REQ-LEGAL-010}})
- **Downstream Requirements**: REQ-CONSENT-020 ({{ISSUE_URL:REQ-CONSENT-020}}), REQ-CONSENT-050 ({{ISSUE_URL:REQ-CONSENT-050}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A: no runtime dependency; a failed build leaves the previous site online

## Implementation Notes

- **Constraints**: Small first-party vanilla JavaScript loaded from the site's own script file, no inline script (SEC-HTTP-2, SEC-OUT-7); banner styles from DESIGN.md; DEP-1 to DEP-8 apply
- **Prohibited Approaches**: Pre-ticked or highlighted Accept; a banner that blocks scrolling or covers the main content; treating any truthy stored value as consent; `innerHTML` insertion of any value
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: First-party script that reads browser storage and decides whether a visitor has consented; a parsing mistake grants consent the visitor never gave (T-72).
- **Required Human Review**: Product review by Nina (banner text and layout, approved with the Datenschutzerklärung); design review against DESIGN.md and `style-guide.html`; privacy review by Ives
- **Open Decisions**: None. Decided in step 10 a third time (`.planning/OPEN_QUESTIONS.md`): PQ-60: fixed text in the templates, written by Ives with the Datenschutzerklärung at implementation and approved by Nina; it must not claim that nothing reaches Google before consent (FR-9.2, DESIGN.md Consent banner); PQ-55 (banner version) is REQ-CONSENT-050; PQ-62 (no banner on the preview) is REQ-CONSENT-040.
