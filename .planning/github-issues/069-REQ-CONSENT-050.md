## Metadata

- **ID**: REQ-CONSENT-050
- **Title**: Store the banner version with the consent choice and ask again when the banner version changes
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 fourth run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Privacy
- **Source / Parent**: REQUIREMENTS.md FR-9.2 (choice stored with the banner version; asked again when the version changes; no record outside the browser); SECURITY.md SEC-PRIV-5 (a choice for another version counts as no choice), SEC-PRIV-7 (Ives raises the version in the same change; step 10 a third time, SQ-23 / PQ-55); T-72, T-73; parent {{ISSUE_URL:REQ-CONSENT-000}}

## Requirement

- **Statement**: The consent banner MUST have one version, defined once in the templates of `dalini/nina-site`; the stored consent choice MUST record the version of the banner it answered; a stored choice for another version MUST count as no choice, so the banner is shown again and consent stays denied until the visitor chooses again; the version MUST be raised in the same change whenever the banner text, the purposes or a Google Analytics setting named in SEC-PRIV-7 changes, and `dalini/nina-site` MUST document this rule where the version is defined; no record of the choice is kept outside the visitor's browser.
- **Rationale**: A choice given to an earlier text or for other settings is not consent to the current ones (T-73); reading an old value as consent would set cookies the visitor never agreed to (T-72).
- **Assumptions**: None
- **Out of Scope**: Showing the banner, the footer link and the strict reading of Accept and Decline (REQ-CONSENT-010); Consent Mode defaults and updates (REQ-CONSENT-020); checking the property's settings against the banner version in the yearly review (REQ-PRIV-030).
- **Design Traceability**: DESIGN.md Components (Consent banner); `style-guide.html` as the rendered reference
- **Architecture Traceability**: Public Site (the visitor's consent choice in browser storage); Site Build templates
- **Security Traceability**: SEC-PRIV-5, SEC-PRIV-7, SEC-OUT-5

## Scope

- **Applies To**: Web Client
- **Components**: Site Build, Public Site
- **Interfaces / Operations**: The banner version constant in the templates; the browser storage entry for the choice; the consent script's reading of that entry
- **Actors**: Visitor (anonymous); maintainer (Ives) when the banner or the settings change
- **Preconditions**: REQ-CONSENT-010, REQ-CONSENT-020
- **Data Classification**: Internal
- **Personal or Regulated Data**: Personal Data (the visitor's consent choice in browser storage)
- **Jurisdiction / Regulatory Scope**: Germany / EU: GDPR and consent for statistics; no article mapping (SECURITY.md)

## Security Context

- **Security Objectives**: Privacy, Integrity
- **Control Layers**: Business-Rule Validation
- **Threat References**: STRIDE Repudiation and Tampering: SECURITY.md Threat Model v3 T-73, T-72
- **Abuse / Misuse Case**: A visitor who accepted an earlier banner text is tracked under changed purposes or settings without being asked again
- **Trust Boundary**: TB-1 (static pages served to visitors), TB-6 (what reaches Google after consent)
- **Untrusted Inputs or Assertions**: The stored consent value and the version in it
- **Authoritative Enforcement Point**: The first-party consent script on each page (version comparison before any consent update)
- **Independent Verification**: Browser tests with stored choices for the current, an older and a malformed version
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP XSS Prevention Cheat Sheet (`REF-XSS`)
- **Mapping Basis**: Other: `REF-XSS` as cited in SECURITY.md SEC-OUT-5.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given a visitor Accepts or Declines, when the choice is stored, then the stored value holds the choice and the current banner version, and on later pages the banner is not shown.
2. **AC-02 — Boundary or failure behavior**: Given a stored Accept for an older banner version, when a page loads, then the banner is shown, `analytics_storage` stays denied, no Google Analytics cookie is set, and after the visitor chooses again the stored value holds the current version.
3. **AC-03 — Boundary or failure behavior**: Given a stored value without a version, with a version that is not the current one, with a higher version than the current one, or with markup in the version, when a page loads, then it counts as no choice, the banner is shown and nothing from the value is rendered as HTML (SEC-OUT-5).
4. **AC-04 — Prohibited behavior**: Given the site's code and the requests a page makes, when they are reviewed after Accept and Decline, then no request sends the choice or its version to the site's server or to any other place, and no server-side record of consent exists.
5. **AC-05 — Additional criterion**: Given the templates of `dalini/nina-site`, when they are reviewed, then the banner version is defined in one place, used by the banner and the consent script alike, and documented there with the rule to raise it whenever the banner text, the purposes or a setting named in SEC-PRIV-7 changes.

## Failure Behavior

- **On Invalid Input**: A stored value whose version is missing, malformed or not the current one counts as no choice: the banner is shown and consent stays denied
- **On Authentication Failure**: N/A: public pages need no sign-in (NFR-2)
- **On Authorization Failure**: N/A: all public pages are read-only and need no account (NFR-2)
- **On Security-Decision Failure**: Deny by default: when the version cannot be compared, consent stays denied
- **On External Dependency Failure**: Google tag blocked or unreachable: the banner and the page keep working (FR-9.2, T-84)
- **On System Error**: Browser storage unavailable or throwing: no consent is assumed, the banner is shown, and the page stays usable
- **Logging / Audit**: N/A: the choice exists only in the visitor's browser; no record of it is kept outside the browser (FR-9.2)
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: Version comparison (current, older, newer, missing, malformed); writing the stored value with the version
- **Integration Tests**: Browser tests on built pages in both languages: Accept, raise the version in a test build, reload, banner shown and consent denied, choose again
- **Security Tests**: Hostile stored values (SEC-OUT-5); network recording that no request carries the choice
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:CONSENT-050/AC-01`; AC-02 → `test:CONSENT-050/AC-02`; AC-03 → `test:CONSENT-050/AC-03`; AC-04 → `test:CONSENT-050/AC-04`; AC-05 → `test:CONSENT-050/AC-05` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model; the last two major versions of Chrome, Edge, Firefox, Safari (macOS, iOS) and Samsung Internet (NFR-4)

## Dependencies

- **Upstream Requirements**: REQ-CONSENT-010 ({{ISSUE_URL:REQ-CONSENT-010}}), REQ-CONSENT-020 ({{ISSUE_URL:REQ-CONSENT-020}})
- **Downstream Requirements**: None
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: Without the version, a visitor's old Accept would count for changed purposes or settings (T-73)

## Implementation Notes

- **Constraints**: Small first-party vanilla JavaScript loaded from the site's own script file, no inline script (SEC-HTTP-2, SEC-OUT-7); the version is part of the templates in `dalini/nina-site`, not editable content (DESIGN.md Consent banner); DEP-1 to DEP-8 apply
- **Prohibited Approaches**: Treating a choice for another version as consent; sending the choice to a server; a version that editors can change through the Editing Interface; `innerHTML` insertion of any stored value
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; read DESIGN.md and `style-guide.html` before UI work; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: It changes the consent decision itself; a comparison mistake turns an old answer into consent for new purposes (T-72, T-73).
- **Required Human Review**: Privacy review by Ives
- **Open Decisions**: None. Decided in step 10 a third time (`.planning/OPEN_QUESTIONS.md`): PQ-55 (= SECURITY.md SQ-23): the stored choice records the banner version; Ives raises it when the text, purposes or Google Analytics settings change, and everyone is asked again; no record outside the browser (FR-9.2, SEC-PRIV-7).
