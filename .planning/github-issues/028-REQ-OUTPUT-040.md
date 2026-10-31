## Metadata

- **ID**: REQ-OUTPUT-040
- **Title**: Fail the build when a generated page contains a script other than the site's own script files
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-OUT-7, SEC-INPUT-3, SEC-PRIV-5 (tag inserted only at runtime); ARCHITECTURE.md DR-3; parent {{ISSUE_URL:REQ-OUTPUT-000}}

## Requirement

- **Statement**: After rendering and before any deploy, the Site Build MUST check every generated page of the production and the preview build and MUST fail and publish nothing when a page contains a script element other than one that loads one of the site's own script files, an inline script, an inline event-handler attribute or a `javascript:` URL, whatever put the markup there (template, content, Instagram caption).
- **Rationale**: `script-src` trusts every script an allowed Google host serves, so injected markup could load a tag configured by someone else (T-76); the build check does not depend on the CSP.
- **Assumptions**: None
- **Out of Scope**: Encoding editor text (REQ-OUTPUT-010); content formats and reserved fields (REQ-CONTENT-080); the CSP itself (REQ-HTTP-020).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build (checks the generated pages, DR-3)
- **Security Traceability**: SEC-OUT-7, SEC-INPUT-3

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build
- **Interfaces / Operations**: Output check over the generated HTML of both builds, before deploy
- **Actors**: Editors (authors of untrusted content); build identity (SEC-CICD-1)
- **Preconditions**: REQ-CONTENT-030, REQ-OUTPUT-010
- **Data Classification**: Multiple
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Output Encoding, Sanitization
- **Threat References**: STRIDE Elevation of Privilege and Tampering: SECURITY.md Threat Model v3 T-76, T-77; CWE-79
- **Abuse / Misuse Case**: Markup that slips past encoding loads an attacker-configured Google tag that passes the CSP
- **Trust Boundary**: TB-4 (Content Store → Site Build)
- **Untrusted Inputs or Assertions**: Generated HTML built from editor content, Instagram captions and templates
- **Authoritative Enforcement Point**: Site Build
- **Independent Verification**: Rechecks in the Site Build, independent of the checks in the editor's browser (DR-3)
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP XSS Prevention Cheat Sheet (`REF-XSS`)
- **Mapping Basis**: Other: `REF-XSS` as cited in SECURITY.md SEC-OUT-7.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given the production and preview output of fixture content, when the check runs, then every `<script>` element loads a script file from the site's own origin and the check passes.
2. **AC-02 — Boundary or failure behavior**: Given a generated page with `<script src="https://www.googletagmanager.com/gtag/js?id=G-TEST">`, one with an inline `<script>`, one with an `onclick` attribute and one with a `javascript:` link, when the build runs, then each build fails, names the page, and the live test site is unchanged.
3. **AC-03 — Boundary or failure behavior**: Given the markup comes from a template, from a content field and from an Instagram caption, when the build runs, then the check fails in each case, whatever put it there.
4. **AC-04 — Prohibited behavior**: Given the production output, when it is searched, then no page contains the Google tag loader itself; only the first-party consent script inserts it at runtime (SEC-PRIV-5).

## Failure Behavior

- **On Invalid Input**: The Site Build fails, names the content item and field, and publishes nothing; the previous site stays online (SEC-INPUT-3)
- **On Authentication Failure**: N/A: the Site Build has no sign-in; it runs as the build identity
- **On Authorization Failure**: N/A: no authorization decision is made in this step
- **On Security-Decision Failure**: Deny by default: if the check cannot complete, the build fails and publishes nothing
- **On External Dependency Failure**: N/A
- **On System Error**: The Site Build fails and publishes nothing; the previous site stays online (SEC-INPUT-3)
- **Logging / Audit**: The build log names the content item identifier and field; it contains no content values beyond identifiers and no secrets (SEC-SECRET-1, SEC-CICD-9)
- **Alerting**: A failed build emails Ives (REQ-PIPELINE-110); Nina sees it in the Editing Interface (REQ-EDIT-090)

## Test Strategy

- **Unit Tests**: HTML scanner: allowed own-origin script files, external script, inline script, event-handler attributes, `javascript:` URLs, edge cases (uppercase tags, entities in URLs)
- **Integration Tests**: Production and preview builds of fixtures with injected markup in each source
- **Security Tests**: Hostile fixture corpus from `REF-XSS`; check that the failure leaves the live test site unchanged
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:OUTPUT-040/AC-01`; AC-02 → `test:OUTPUT-040/AC-02`; AC-03 → `test:OUTPUT-040/AC-03`; AC-04 → `test:OUTPUT-040/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Site Build (Hugo) run locally against fixture content in the documented content model, including hostile fixtures

## Dependencies

- **Upstream Requirements**: REQ-CONTENT-030 ({{ISSUE_URL:REQ-CONTENT-030}}), REQ-OUTPUT-010 ({{ISSUE_URL:REQ-OUTPUT-010}})
- **Downstream Requirements**: REQ-CONSENT-020 ({{ISSUE_URL:REQ-CONSENT-020}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: A failure of the check blocks publishing until the content is fixed; the previous site stays online

## Implementation Notes

- **Constraints**: Hugo, pinned at a version without known advisories (`REF-HUGO-ADV`, `REF-HUGO-SEC`, REQ-PIPELINE-030); DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Checks that run only in the editor's browser (DR-3); logging content values or secrets
- **Implementation Guidance**: Parse the HTML with a real parser rather than regular expressions; treat an unparsable page as a failure.
- **AI Development Guidance**: Follow CLAUDE.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: The last barrier against a Google or Tag Manager script injected into a page (T-76) must parse real HTML output robustly for both builds.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
