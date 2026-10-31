## Metadata

- **ID**: REQ-CONSENT-000
- **Title**: Visitor statistics with Google Analytics and the consent banner
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: REQUIREMENTS.md FR-9.2, NFR-3; SECURITY.md SEC-PRIV-5, SEC-PRIV-7, SEC-HTTP-2, SEC-OUT-5, SEC-OUT-7; ARCHITECTURE.md Public Site, DR-6; DESIGN.md Components (Consent banner); parent {{ISSUE_URL:REQ-EPIC-001}}

## Requirement

- **Statement**: The leaf issues of this workstream MUST show the consent banner, run the Google tag only with Consent Mode v2 as SEC-PRIV-5 requires, keep its cookies on `nina-laaf.de`, keep Google Analytics off the preview and editing hosts, and administer the property under SEC-PRIV-7.
- **Rationale**: Groups leaf issues that share components, rules and dependencies so they can be planned and reviewed together.
- **Assumptions**: None
- **Out of Scope**: Implementation work in this workstream issue itself; behavior outside the listed leaves. Recording the banner version and asking again when it changes, and keeping a record of consent outside the browser (decided in step 10 a third time, PQ-55; drafted by the next step 9 run, SECURITY.md SQ-23).
- **Design Traceability**: DESIGN.md Components (Consent banner, Buttons, Focus state), Accessibility
- **Architecture Traceability**: Public Site; Site Build; DR-6; TB-6
- **Security Traceability**: SEC-PRIV-5, SEC-PRIV-7, SEC-HTTP-2, SEC-OUT-5

## Scope

- **Applies To**: Multiple
- **Components**: Public Site, Site Build templates, the Google Analytics property (external)
- **Interfaces / Operations**: See the leaf issues
- **Actors**: See the leaf issues
- **Preconditions**: None
- **Data Classification**: Multiple
- **Personal or Regulated Data**: See the leaf issues
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Multiple
- **Control Layers**: See the leaf issues
- **Threat References**: See the leaf issues
- **Abuse / Misuse Case**: See the leaf issues
- **Trust Boundary**: See the leaf issues
- **Untrusted Inputs or Assertions**: See the leaf issues
- **Authoritative Enforcement Point**: See the leaf issues
- **Independent Verification**: See the leaf issues
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: N/A
- **Mapping Basis**: Workstream issue; mappings are made per leaf issue.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given every leaf issue listed below is closed, when the workstream is reviewed, then each requirement it groups is COVERED in the ISSUE_PLAN.md coverage table or is listed there as BLOCKED or PARTIALLY COVERED with its open question.
2. **AC-02 — Boundary or failure behavior**: Given a leaf issue is still open, when the workstream is reviewed for closing, then the workstream stays open.
3. **AC-03 — Prohibited behavior**: Given any leaf issue of this workstream, when it is closed, then its tests, error handling, security and accessibility work are part of that leaf and none is deferred to a separate cleanup issue.

## Failure Behavior

- **On Invalid Input**: Per leaf issue
- **On Authentication Failure**: Per leaf issue
- **On Authorization Failure**: Per leaf issue
- **On Security-Decision Failure**: Per leaf issue; deny by default
- **On External Dependency Failure**: Per leaf issue
- **On System Error**: Per leaf issue
- **Logging / Audit**: Per leaf issue
- **Alerting**: Per leaf issue

## Test Strategy

- **Unit Tests**: Per leaf issue
- **Integration Tests**: Per leaf issue
- **Security Tests**: Per leaf issue
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → coverage review of ISSUE_PLAN.md; AC-02 → workstream close checklist; AC-03 → review of each leaf's pull request
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Per leaf issue

## Dependencies

- **Upstream Requirements**: None
- **Downstream Requirements**: REQ-CONSENT-010 ({{ISSUE_URL:REQ-CONSENT-010}}), REQ-PRIV-030 ({{ISSUE_URL:REQ-PRIV-030}}), REQ-CONSENT-020 ({{ISSUE_URL:REQ-CONSENT-020}}), REQ-CONSENT-030 ({{ISSUE_URL:REQ-CONSENT-030}}), REQ-CONSENT-040 ({{ISSUE_URL:REQ-CONSENT-040}})
- **External Dependencies**: See the leaf issues
- **Dependency Assumptions**: See the leaf issues
- **Failure Impact**: See the leaf issues

## Implementation Notes

- **Constraints**: DEP-1 to DEP-8 apply to every dependency added; ARCHITECTURE.md DR-1 to DR-9
- **Prohibited Approaches**: Implementation work in the workstream issue itself; it only groups leaves
- **Implementation Guidance**: Work the leaves in ISSUE_PLAN.md Topological Creation Order
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement each leaf as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request.
- **Required Human Review**: Per leaf issue
- **Open Decisions**: None. Answered in step 10 a third time (`.planning/OPEN_QUESTIONS.md`): PQ-55: the stored choice records the banner version; Ives raises it when the text, purposes or Google Analytics settings change, and everyone is asked again; no record outside the browser (FR-9.2, SEC-PRIV-7); PQ-60: fixed text in the templates, written by Ives with the Datenschutzerklärung at implementation and approved by Nina; it must not claim that nothing reaches Google before consent (FR-9.2, DESIGN.md Consent banner); PQ-61: the `cookie_domain` value is verified at implementation; if none gives host-only cookies, cookies for `nina-laaf.de` are an accepted risk (SEC-PRIV-5); PQ-62: the preview build leaves out the consent script and its footer link (FR-9.2, SEC-PRIV-5). Parts that were excluded as blocked scope are drafted by the next step 9 run.

## Sub-issues

- [ ] REQ-CONSENT-010 {{ISSUE_URL:REQ-CONSENT-010}}
- [ ] REQ-PRIV-030 {{ISSUE_URL:REQ-PRIV-030}}
- [ ] REQ-CONSENT-020 {{ISSUE_URL:REQ-CONSENT-020}}
- [ ] REQ-CONSENT-030 {{ISSUE_URL:REQ-CONSENT-030}}
- [ ] REQ-CONSENT-040 {{ISSUE_URL:REQ-CONSENT-040}}
