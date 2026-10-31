## Metadata

- **ID**: REQ-EPIC-001
- **Title**: Build the bilingual nina-laaf.de website from the specification
- **Version**: 0.3.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-1.1 to FR-11.4 and NFR-1 to NFR-4; SECURITY.md Security Rules, DEP-1 to DEP-8 and Threat Model v3 (T-1 to T-85); ARCHITECTURE.md Architecture and DR-1 to DR-9; DESIGN.md

## Requirement

- **Statement**: The system MUST implement every requirement in REQUIREMENTS.md and every rule in SECURITY.md through the components and dependency rules of ARCHITECTURE.md and the design language of DESIGN.md; this epic is done when every sub-issue is closed and every row of the coverage table in `.planning/github-issues/ISSUE_PLAN.md` is COVERED or OUT OF SCOPE.
- **Rationale**: nina-laaf.de presents Nina Laaf's work to curators, galleries, collectors, press and the public and makes it easy to contact her (REQUIREMENTS.md Required Requirement Inputs).
- **Assumptions**: None
- **Out of Scope**: FR-7.3 contact form (not in this release); newsletter (FR-7.4). The blocked scope of ISSUE_PLAN.md (PQ-54 to PQ-59) is answered in step 10 a third time and gets sub-issues in the next step 9 run (PQ-59 removes scope: no order replaces the year order).
- **Design Traceability**: DESIGN.md (all sections); `style-guide.html`; `logo.svg` (provisional)
- **Architecture Traceability**: All components (Editing Interface, Content Store, Site Build, Static Hosting, Public Site, Instagram Feed Integration); DR-1 to DR-9
- **Security Traceability**: SECURITY.md Security Rules, DEP-1 to DEP-8, Threat Model v3 (T-1 to T-85); SQ-22 and SQ-23 (ISSUE_PLAN.md PQ-54, PQ-55) decided in step 10 a third time

## Scope

- **Applies To**: Multiple
- **Components**: Editing Interface (with its OAuth helper), Content Store, Site Build, Static Hosting, Public Site, Instagram Feed Integration; the Google Analytics property (external)
- **Interfaces / Operations**: All public pages (DE and EN), the editing workflows, the preview site, the build and publish pipeline, the Instagram feed, the VPS
- **Actors**: Visitor; editors and owners (Nina, Ives); maintainer (Ives); build/deploy identity; Instagram; GitHub; Google (Google tag and Analytics); VPS provider
- **Preconditions**: None for the decomposed sub-issues; the blocked scope (PQ-54 to PQ-59, answered in step 10 a third time) needs the next step 9 run to draft it
- **Data Classification**: Multiple
- **Personal or Regulated Data**: Personal Data (editor identities and credentials, Nina's published email address, location metadata in uploaded photos, visitor IP addresses in access logs, visitor and browser data sent to Google Analytics, the consent choice in the visitor's browser)
- **Jurisdiction / Regulatory Scope**: Germany / EU: GDPR; Impressum and Datenschutzerklärung (FR-9.1); wording drafted with a German legal-text generator and approved by Nina before release (SECURITY.md)

## Security Context

- **Security Objectives**: Multiple
- **Control Layers**: Architecture, Authentication, Authorization, Session Management, Input Validation, Output Encoding, Sanitization, Data Protection, Logging and Monitoring, Availability, Supply Chain
- **Threat References**: SECURITY.md Threat Model v3 (2026-10-31), STRIDE, T-1 to T-85
- **Abuse / Misuse Case**: See each sub-issue and SECURITY.md Threats
- **Trust Boundary**: TB-1 to TB-6 (SECURITY.md Required Security Inputs)
- **Untrusted Inputs or Assertions**: Editor content and uploads, direct writes to the Content Store, legacy content (DF-3), Instagram data (DF-8), visitor requests, the Google tag served live by Google and the property settings that drive it (DF-14, DF-15)
- **Authoritative Enforcement Point**: Per sub-issue: Site Build, Static Hosting, Content Store (GitHub), Editing Interface
- **Independent Verification**: Content rules are enforced by the Editing Interface and rechecked by the Site Build (DR-3)
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A at epic level; GDPR is referenced in the privacy and legal sub-issues without article mapping (SECURITY.md)
- **Other**: N/A
- **Mapping Basis**: Parent issue; mappings are made per sub-issue.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given every sub-issue is closed, when the coverage table in ISSUE_PLAN.md is reviewed against REQUIREMENTS.md and SECURITY.md, then every FR-*, NFR-* and SEC-* row is COVERED or OUT OF SCOPE.
2. **AC-02 — Boundary or failure behavior**: Given a row is BLOCKED or PARTIALLY COVERED, when the epic is reviewed for closing or release, then it stays open until the question is answered and sub-issues for the remaining scope exist and are closed.
3. **AC-03 — Prohibited behavior**: Given any sub-issue, when it is closed, then its tests, error handling, security and accessibility work are part of that sub-issue and none is deferred to a separate cleanup issue.
4. **AC-04 — Additional criterion**: Given the release gate in ISSUE_PLAN.md (Blocked Scope), when a first release is proposed, then it is refused while any gate item is open.

## Failure Behavior

- **On Invalid Input**: Per sub-issue
- **On Authentication Failure**: Per sub-issue; Editing Interface sign-in: REQ-AUTHN-000
- **On Authorization Failure**: Per sub-issue; deny by default
- **On Security-Decision Failure**: Per sub-issue; deny by default
- **On External Dependency Failure**: Per sub-issue (GitHub, Instagram, VPS)
- **On System Error**: Per sub-issue; a failed build leaves the previous site online (SEC-INPUT-3)
- **Logging / Audit**: Per sub-issue
- **Alerting**: Per sub-issue (failed builds email Ives, REQ-PIPELINE-110; email alerts from the VPS go through the local mail relay agent, PQ-56, decided in step 10 a third time and drafted by the next step 9 run)

## Test Strategy

- **Unit Tests**: Per sub-issue
- **Integration Tests**: Per sub-issue
- **Security Tests**: Per sub-issue
- **Compliance Tests / Evidence**: Impressum and Datenschutzerklärung reachable from every page and their content reviewed (REQ-LEGAL-010); consent banner and Consent Mode browser tests (REQ-CONSENT-010, REQ-CONSENT-020); network recordings (REQ-PRIV-010); access-log retention (REQ-PRIV-020); Google Analytics property review (REQ-PRIV-030)
- **Acceptance-Criteria Traceability**: AC-01 → coverage review of ISSUE_PLAN.md; AC-02 → epic close checklist; AC-03 → review of each sub-issue's pull request; AC-04 → release checklist against the release gate
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Per sub-issue

## Dependencies

- **Upstream Requirements**: None
- **Downstream Requirements**: All sub-issues listed below
- **External Dependencies**: GitHub (repositories, OAuth, Actions); Instagram API with Instagram Login; Google Analytics (Google tag, Consent Mode v2); VPS provider in Germany; Let's Encrypt; Sveltia CMS; Hugo; Deno
- **Dependency Assumptions**: See each sub-issue
- **Failure Impact**: See each sub-issue

## Implementation Notes

- **Constraints**: DEP-1 to DEP-8 apply to every dependency added; ARCHITECTURE.md DR-1 to DR-9
- **Prohibited Approaches**: Issues such as "Build the backend", "Add security" or "Write tests"; client-side-only enforcement of content rules (DR-3)
- **Implementation Guidance**: Create order and dependencies: ISSUE_PLAN.md Topological Creation Order
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement each leaf as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request.
- **Required Human Review**: Product (Nina), security, privacy/legal (Impressum, Datenschutzerklärung) and architecture review across the epic
- **Open Decisions**: None. Answered in step 10 a third time (`.planning/OPEN_QUESTIONS.md`): PQ-54: Ives installs the Editing Interface files by hand from a tagged, verified release of `dalini/nina-site`; root-owned, read-only to nginx and the helper; no deploy key writes them (SEC-CICD-2); PQ-55: the stored choice records the banner version; Ives raises it when the text, purposes or Google Analytics settings change, and everyone is asked again; no record outside the browser (FR-9.2, SEC-PRIV-7); PQ-56: VPS alerts go through a local mail relay agent that runs under its own account and alone holds the relay credential; the jobs hand mail to local `sendmail` (SEC-HTTP-5, SEC-SECRET-1); PQ-57: the slug is generated from title and start year at first save (e.g. `faltungen-2025`) and never changes; duplicate slugs fail the publish, naming both entries (FR-4.7, SEC-INPUT-6); PQ-58: materials and dimensions come from the work-level legacy fields, else from a first caption in the form "materials | dimensions | year", else stay empty; a migration report lists what Nina must complete and values that break FR-3.3 (FR-11.1); PQ-59: no order replaces the year order: newest year first, the manual position orders works within a year only (FR-3.1); PQ-60: fixed text in the templates, written by Ives with the Datenschutzerklärung at implementation and approved by Nina; it must not claim that nothing reaches Google before consent (FR-9.2, DESIGN.md Consent banner); PQ-61: the `cookie_domain` value is verified at implementation; if none gives host-only cookies, cookies for `nina-laaf.de` are an accepted risk (SEC-PRIV-5); PQ-62: the preview build leaves out the consent script and its footer link (FR-9.2, SEC-PRIV-5); PQ-63: German, informal: "Deine Änderungen sind noch nicht online." and "Bitte prüfe: <Eintrag>, Feld <Feld>."; a strip above the CMS on `surface` with an `error` left border (FR-10.8, DESIGN.md Publish status banner); PQ-64: year, title (e.g. "2019  Raumforderung") (FR-5.3); PQ-65: numeric dates: DE `DD.MM.YYYY`, EN `YYYY-MM-DD`; ranges with both full dates and an en dash (FR-1.8); PQ-66: the viewer stops at the ends (Previous/Next disabled there) and shows the position, e.g. "3 / 7" (FR-3.11). Parts that were excluded as blocked scope are drafted by the next step 9 run. The excluded replacing order is dropped (PQ-59), not drafted. Deferred by Ives: the final logo mark (DESIGN.md).

## Sub-issues

- [ ] REQ-CONTENT-000 {{ISSUE_URL:REQ-CONTENT-000}}
- [ ] REQ-OUTPUT-000 {{ISSUE_URL:REQ-OUTPUT-000}}
- [ ] REQ-NAV-000 {{ISSUE_URL:REQ-NAV-000}}
- [ ] REQ-WORKS-000 {{ISSUE_URL:REQ-WORKS-000}}
- [ ] REQ-IMAGES-000 {{ISSUE_URL:REQ-IMAGES-000}}
- [ ] REQ-EXHIBITIONS-000 {{ISSUE_URL:REQ-EXHIBITIONS-000}}
- [ ] REQ-CV-000 {{ISSUE_URL:REQ-CV-000}}
- [ ] REQ-INSTAGRAM-000 {{ISSUE_URL:REQ-INSTAGRAM-000}}
- [ ] REQ-CONSENT-000 {{ISSUE_URL:REQ-CONSENT-000}}
- [ ] REQ-HTTP-000 {{ISSUE_URL:REQ-HTTP-000}}
- [ ] REQ-STORE-000 {{ISSUE_URL:REQ-STORE-000}}
- [ ] REQ-PIPELINE-000 {{ISSUE_URL:REQ-PIPELINE-000}}
- [ ] REQ-AUTHN-000 {{ISSUE_URL:REQ-AUTHN-000}}
- [ ] REQ-EDIT-000 {{ISSUE_URL:REQ-EDIT-000}}
- [ ] REQ-MIGRATION-000 {{ISSUE_URL:REQ-MIGRATION-000}}
- [ ] REQ-LAYOUT-010 {{ISSUE_URL:REQ-LAYOUT-010}}
- [ ] REQ-HOME-010 {{ISSUE_URL:REQ-HOME-010}}
- [ ] REQ-HOME-020 {{ISSUE_URL:REQ-HOME-020}}
- [ ] REQ-TEXTS-010 {{ISSUE_URL:REQ-TEXTS-010}}
- [ ] REQ-DOWNLOADS-010 {{ISSUE_URL:REQ-DOWNLOADS-010}}
- [ ] REQ-CONTACT-010 {{ISSUE_URL:REQ-CONTACT-010}}
- [ ] REQ-LEGAL-010 {{ISSUE_URL:REQ-LEGAL-010}}
- [ ] REQ-HOST-010 {{ISSUE_URL:REQ-HOST-010}}
- [ ] REQ-HOST-020 {{ISSUE_URL:REQ-HOST-020}}
- [ ] REQ-HOST-030 {{ISSUE_URL:REQ-HOST-030}}
- [ ] REQ-PRIV-020 {{ISSUE_URL:REQ-PRIV-020}}
- [ ] REQ-PERF-010 {{ISSUE_URL:REQ-PERF-010}}
- [ ] REQ-PRIV-010 {{ISSUE_URL:REQ-PRIV-010}}
