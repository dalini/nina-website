## Metadata

- **ID**: REQ-EPIC-001
- **Title**: Build the bilingual nina-laaf.de website from the specification
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-1.1 to FR-11.4 and NFR-1 to NFR-4; SECURITY.md Security Rules, DEP-1 to DEP-8 and Threat Model v2 (T-1 to T-68); ARCHITECTURE.md Architecture and DR-1 to DR-9; DESIGN.md

## Requirement

- **Statement**: The system MUST implement every requirement in REQUIREMENTS.md and every rule in SECURITY.md through the components and dependency rules of ARCHITECTURE.md and the design language of DESIGN.md; this epic is done when every sub-issue is closed and every row of the coverage table in `.planning/github-issues/ISSUE_PLAN.md` is COVERED or OUT OF SCOPE.
- **Rationale**: nina-laaf.de presents Nina Laaf's work to curators, galleries, collectors, press and the public and makes it easy to contact her (REQUIREMENTS.md Required Requirement Inputs).
- **Assumptions**: None
- **Out of Scope**: FR-7.3 contact form (not in this release); newsletter (FR-7.4); analytics in pages (SEC-PRIV-5). Blocked scope (ISSUE_PLAN.md Blocked Scope, PQ-29 to PQ-39) has no sub-issues yet and is decomposed in a later step 9 run.
- **Design Traceability**: DESIGN.md (all sections); `style-guide.html`; `logo.svg` (provisional)
- **Architecture Traceability**: All components (Editing Interface, Content Store, Site Build, Static Hosting, Public Site, Instagram Feed Integration); DR-1 to DR-9
- **Security Traceability**: SECURITY.md Security Rules, DEP-1 to DEP-8, Threat Model v2 (T-1 to T-68), Open Security Questions SQ-16 to SQ-21

## Scope

- **Applies To**: Multiple
- **Components**: Editing Interface, Content Store, Site Build, Static Hosting, Public Site, Instagram Feed Integration
- **Interfaces / Operations**: All public pages (DE and EN), the editing workflows, the preview site, the build and publish pipeline, the Instagram feed, the VPS
- **Actors**: Visitor; editors and owners (Nina, Ives); maintainer (Ives); build/deploy identity; Instagram; GitHub; VPS provider
- **Preconditions**: None for the decomposed sub-issues; the blocked scope needs answers to PQ-29 to PQ-39 (step 10) and a step 9 re-run
- **Data Classification**: Multiple
- **Personal or Regulated Data**: Personal Data (editor identities and credentials, Nina's published email address, location metadata in uploaded photos, visitor IP addresses in access logs)
- **Jurisdiction / Regulatory Scope**: Germany / EU: GDPR; Impressum and Datenschutzerklärung (FR-9.1); wording drafted with a German legal-text generator and approved by Nina before release (SECURITY.md)

## Security Context

- **Security Objectives**: Multiple
- **Control Layers**: Architecture, Authentication, Authorization, Session Management, Input Validation, Output Encoding, Sanitization, Data Protection, Logging and Monitoring, Availability, Supply Chain
- **Threat References**: SECURITY.md Threat Model v2 (2026-10-31), STRIDE, T-1 to T-68
- **Abuse / Misuse Case**: See each sub-issue and SECURITY.md Threats
- **Trust Boundary**: TB-1 to TB-5 (SECURITY.md Required Security Inputs)
- **Untrusted Inputs or Assertions**: Editor content and uploads, direct writes to the Content Store, legacy content (DF-3), Instagram data (DF-8), visitor requests
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
- **On Authentication Failure**: Per sub-issue; Editing Interface sign-in is blocked scope (PQ-34)
- **On Authorization Failure**: Per sub-issue; deny by default
- **On Security-Decision Failure**: Per sub-issue; deny by default
- **On External Dependency Failure**: Per sub-issue (GitHub, Instagram, VPS)
- **On System Error**: Per sub-issue; a failed build leaves the previous site online (SEC-INPUT-3)
- **Logging / Audit**: Per sub-issue
- **Alerting**: Per sub-issue (failed builds email Ives, REQ-PIPELINE-110)

## Test Strategy

- **Unit Tests**: Per sub-issue
- **Integration Tests**: Per sub-issue
- **Security Tests**: Per sub-issue
- **Compliance Tests / Evidence**: Impressum and Datenschutzerklärung reachable from every page (REQ-LEGAL-010); network recordings (REQ-PRIV-010); access-log retention (REQ-PRIV-020)
- **Acceptance-Criteria Traceability**: AC-01 → coverage review of ISSUE_PLAN.md; AC-02 → epic close checklist; AC-03 → review of each sub-issue's pull request; AC-04 → release checklist against the release gate
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Per sub-issue

## Dependencies

- **Upstream Requirements**: None
- **Downstream Requirements**: All sub-issues listed below
- **External Dependencies**: GitHub (repositories, OAuth, Actions); Instagram API with Instagram Login; VPS provider in Germany; Sveltia CMS; Hugo
- **Dependency Assumptions**: See each sub-issue
- **Failure Impact**: See each sub-issue

## Implementation Notes

- **Constraints**: DEP-1 to DEP-8 apply to every dependency added; ARCHITECTURE.md DR-1 to DR-9
- **Prohibited Approaches**: Issues such as "Build the backend", "Add security" or "Write tests"; client-side-only enforcement of content rules (DR-3)
- **Implementation Guidance**: Create order and dependencies: ISSUE_PLAN.md Topological Creation Order
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement each leaf as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request.
- **Required Human Review**: Product (Nina), security, privacy/legal (Impressum, Datenschutzerklärung) and architecture review across the epic
- **Open Decisions**: Blocking: PQ-29 to PQ-39 (ISSUE_PLAN.md; PQ-29 to PQ-34 are SECURITY.md SQ-16 to SQ-21). Non-blocking: PQ-40 to PQ-53. Deferred by Ives: the final logo mark (DESIGN.md) and the test command (CLAUDE.md, step 11).

## Sub-issues

- [ ] REQ-CONTENT-000 {{ISSUE_URL:REQ-CONTENT-000}}
- [ ] REQ-OUTPUT-000 {{ISSUE_URL:REQ-OUTPUT-000}}
- [ ] REQ-NAV-000 {{ISSUE_URL:REQ-NAV-000}}
- [ ] REQ-WORKS-000 {{ISSUE_URL:REQ-WORKS-000}}
- [ ] REQ-IMAGES-000 {{ISSUE_URL:REQ-IMAGES-000}}
- [ ] REQ-EXHIBITIONS-000 {{ISSUE_URL:REQ-EXHIBITIONS-000}}
- [ ] REQ-CV-000 {{ISSUE_URL:REQ-CV-000}}
- [ ] REQ-INSTAGRAM-000 {{ISSUE_URL:REQ-INSTAGRAM-000}}
- [ ] REQ-HTTP-000 {{ISSUE_URL:REQ-HTTP-000}}
- [ ] REQ-STORE-000 {{ISSUE_URL:REQ-STORE-000}}
- [ ] REQ-PIPELINE-000 {{ISSUE_URL:REQ-PIPELINE-000}}
- [ ] REQ-EDIT-000 {{ISSUE_URL:REQ-EDIT-000}}
- [ ] REQ-MIGRATION-000 {{ISSUE_URL:REQ-MIGRATION-000}}
- [ ] REQ-LAYOUT-010 {{ISSUE_URL:REQ-LAYOUT-010}}
- [ ] REQ-HOME-010 {{ISSUE_URL:REQ-HOME-010}}
- [ ] REQ-HOME-020 {{ISSUE_URL:REQ-HOME-020}}
- [ ] REQ-PERF-010 {{ISSUE_URL:REQ-PERF-010}}
- [ ] REQ-TEXTS-010 {{ISSUE_URL:REQ-TEXTS-010}}
- [ ] REQ-DOWNLOADS-010 {{ISSUE_URL:REQ-DOWNLOADS-010}}
- [ ] REQ-CONTACT-010 {{ISSUE_URL:REQ-CONTACT-010}}
- [ ] REQ-LEGAL-010 {{ISSUE_URL:REQ-LEGAL-010}}
- [ ] REQ-PRIV-010 {{ISSUE_URL:REQ-PRIV-010}}
- [ ] REQ-PRIV-020 {{ISSUE_URL:REQ-PRIV-020}}
- [ ] REQ-HOST-010 {{ISSUE_URL:REQ-HOST-010}}
- [ ] REQ-HOST-020 {{ISSUE_URL:REQ-HOST-020}}
