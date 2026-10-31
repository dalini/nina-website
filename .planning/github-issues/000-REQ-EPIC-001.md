## Metadata

- **ID**: REQ-EPIC-001
- **Title**: Build the bilingual nina-laaf.de website from the specification
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-1.1 to FR-11.4 and NFR-1 to NFR-4; SECURITY.md SEC-* and DEP-1 to DEP-8; ARCHITECTURE.md Architecture and DR-1 to DR-9; DESIGN.md

## Requirement

- **Statement**: The system MUST implement every requirement in REQUIREMENTS.md and every rule in SECURITY.md through the components and dependency rules of ARCHITECTURE.md and the design language of DESIGN.md; this epic is done when every sub-issue is closed and every row of the coverage table in `.planning/github-issues/ISSUE_PLAN.md` is COVERED or OUT OF SCOPE.
- **Rationale**: nina-laaf.de presents Nina Laaf's work to curators, galleries, collectors, press and the public and makes it easy to contact her (REQUIREMENTS.md Required Requirement Inputs).
- **Assumptions**: None. Step 10 (`.planning/OPEN_QUESTIONS.md`) replaced the earlier ASSUMPTIONs: the Content Store is `dalini/nina-content` without large-file storage; builds run on a 5-minute poll and nightly; the maintainer is Ives.
- **Out of Scope**: FR-7.3 contact form (not in this release); newsletter (FR-7.4: none); analytics scripts (SEC-PRIV-5 allows only statistics from server logs). Scope unblocked by step 10 gets sub-issues in the step 9 re-run.
- **Design Traceability**: DESIGN.md (all sections); `style-guide.html`; `logo.svg`
- **Architecture Traceability**: All components (Editing Interface, Content Store, Site Build, Static Hosting, Public Site, Instagram Feed Integration); DR-1 to DR-9
- **Security Traceability**: SECURITY.md Security Rules, Dependency Security Rules DEP-1 to DEP-8, Threat Model v1 (T-1 to T-42)

## Scope

- **Applies To**: Multiple
- **Components**: Editing Interface, Content Store, Site Build, Static Hosting, Public Site, Instagram Feed Integration
- **Interfaces / Operations**: All public pages (DE and EN), the editing workflows, the build and publish pipeline, the Instagram feed
- **Actors**: Visitor; Editors (Nina and Ives); Owners (Nina and Ives); build/deploy identity; maintainer (Ives); Instagram
- **Preconditions**: Step 10 answered all blocking questions (PQ-1 to PQ-15); the step 9 re-run decomposes the unblocked scope before the CREATE run (prompts/09-github-epic.md)
- **Data Classification**: Multiple
- **Personal or Regulated Data**: Personal Data (editor identities, Nina's published email address, location metadata in uploaded photos)
- **Jurisdiction / Regulatory Scope**: Germany / EU: GDPR; Impressum and Datenschutzerklärung (FR-9.1); wording drafted with a German legal-text generator and approved by Nina before release (SECURITY.md)

## Security Context

- **Security Objectives**: Multiple
- **Control Layers**: Architecture, Authentication, Authorization, Session Management, Input Validation, Output Encoding, Data Protection, Logging and Monitoring, Availability, Supply Chain
- **Threat References**: SECURITY.md Threat Model v1 (2026-10-31), STRIDE, T-1 to T-42
- **Abuse / Misuse Case**: See each sub-issue and SECURITY.md Threats
- **Trust Boundary**: TB-1 to TB-5 (SECURITY.md Required Security Inputs)
- **Untrusted Inputs or Assertions**: Editor content and uploads, legacy content (DF-3), Instagram data (DF-8), visitor requests
- **Authoritative Enforcement Point**: Per sub-issue: Site Build, Static Hosting, Content Store, Editing Interface
- **Independent Verification**: Content rules are enforced by the Editing Interface and rechecked by the Site Build (DR-3)
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: N/A
- **Mapping Basis**: Parent issue; mappings are made per sub-issue.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given every sub-issue is closed, when the coverage table in ISSUE_PLAN.md is reviewed against REQUIREMENTS.md and SECURITY.md, then every FR-*, NFR-* and SEC-* row is COVERED or OUT OF SCOPE with a recorded decision.
2. **AC-02 — Boundary or failure behavior**: Given a requirement row is PARTIALLY COVERED or not yet decomposed into sub-issues, when the epic is reviewed for closing, then the epic stays open until sub-issues for the remaining scope exist and are closed.
3. **AC-03 — Prohibited behavior**: Given any sub-issue, when it is closed, then its tests, error handling, security and accessibility work are part of that sub-issue and none is deferred to a separate cleanup issue.

## Failure Behavior

- **On Invalid Input**: Per sub-issue
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication (SECURITY.md SEC-AUTHN-1 to -3) is decomposed in the step 9 re-run
- **On Authorization Failure**: N/A
- **On Security-Decision Failure**: Per sub-issue; deny by default
- **On External Dependency Failure**: N/A
- **On System Error**: Per sub-issue
- **Logging / Audit**: Per sub-issue
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: Per sub-issue
- **Integration Tests**: Per sub-issue
- **Security Tests**: Per sub-issue
- **Compliance Tests / Evidence**: Impressum and Datenschutzerklärung reachable from every page (REQ-LEGAL-010); network test before consent (REQ-PRIV-010)
- **Acceptance-Criteria Traceability**: AC-01 → coverage review of ISSUE_PLAN.md; AC-02 → epic close checklist; AC-03 → review of each sub-issue's pull request
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Per sub-issue

## Dependencies

- **Upstream Requirements**: None
- **Downstream Requirements**: All sub-issues listed below
- **External Dependencies**: Instagram; VPS provider in Germany (nginx), GitHub Actions, Sveltia CMS and Hugo (ARCHITECTURE.md)
- **Dependency Assumptions**: See each sub-issue
- **Failure Impact**: See each sub-issue

## Implementation Notes

- **Constraints**: DEP-1 to DEP-8 apply to every dependency added; ARCHITECTURE.md DR-1 to DR-9
- **Prohibited Approaches**: Issues such as "Build the backend", "Add security" or "Write tests"; client-side-only enforcement of content rules (DR-3)
- **Implementation Guidance**: Create order and dependencies: ISSUE_PLAN.md Topological Creation Order
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request.
- **Required Human Review**: Product (Nina), security, privacy/legal (Impressum, Datenschutzerklärung) and architecture review across the epic
- **Open Decisions**: None open. PQ-1 to PQ-28 were answered in step 10 (`.planning/OPEN_QUESTIONS.md`). Deferred by Ives: the final logo mark (DESIGN.md) and the test command (CLAUDE.md, step 11).

## Sub-issues

- [ ] REQ-CONTENT-000 {{ISSUE_URL:REQ-CONTENT-000}}
- [ ] REQ-OUTPUT-000 {{ISSUE_URL:REQ-OUTPUT-000}}
- [ ] REQ-NAV-000 {{ISSUE_URL:REQ-NAV-000}}
- [ ] REQ-WORKS-000 {{ISSUE_URL:REQ-WORKS-000}}
- [ ] REQ-HTTP-000 {{ISSUE_URL:REQ-HTTP-000}}
- [ ] REQ-PIPELINE-000 {{ISSUE_URL:REQ-PIPELINE-000}}
- [ ] REQ-LEGAL-010 {{ISSUE_URL:REQ-LEGAL-010}}
- [ ] REQ-TEXTS-010 {{ISSUE_URL:REQ-TEXTS-010}}
- [ ] REQ-CV-010 {{ISSUE_URL:REQ-CV-010}}
- [ ] REQ-DOWNLOADS-010 {{ISSUE_URL:REQ-DOWNLOADS-010}}
- [ ] REQ-CONTACT-010 {{ISSUE_URL:REQ-CONTACT-010}}
- [ ] REQ-INSTAGRAM-010 {{ISSUE_URL:REQ-INSTAGRAM-010}}
- [ ] REQ-PRIV-010 {{ISSUE_URL:REQ-PRIV-010}}
