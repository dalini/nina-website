## Metadata

- **ID**: REQ-CONTENT-000
- **Title**: Content model and build-time content rules
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: ARCHITECTURE.md Data model expectations, DR-2, DR-3, DR-4; REQUIREMENTS.md FR-1.4, FR-1.5, FR-3.2 to FR-3.8, FR-5, FR-6, FR-7.1, FR-7.2, FR-10.3, FR-10.6, FR-11.2; parent {{ISSUE_URL:REQ-EPIC-001}}

## Requirement

- **Statement**: The leaf issues of this workstream MUST define the documented content model and make the Site Build enforce its rules (FR-10.6 recheck, SEC-INPUT-3, SEC-INPUT-4) so that templates only render (DR-3).
- **Rationale**: Groups leaf issues that share components and dependencies so they can be planned and reviewed together.
- **Assumptions**: None
- **Out of Scope**: Behavior outside the listed leaf issues; scope unblocked by step 10 is decomposed in the step 9 re-run (ISSUE_PLAN.md).
- **Design Traceability**: N/A
- **Architecture Traceability**: Content Store, Site Build; DR-2, DR-3, DR-4
- **Security Traceability**: SEC-INPUT-3, SEC-INPUT-4

## Scope

- **Applies To**: Multiple
- **Components**: Content Store, Site Build
- **Interfaces / Operations**: See leaf issues
- **Actors**: See leaf issues
- **Preconditions**: None
- **Data Classification**: Multiple
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Multiple
- **Control Layers**: See leaf issues
- **Threat References**: See leaf issues
- **Abuse / Misuse Case**: See leaf issues
- **Trust Boundary**: See leaf issues
- **Untrusted Inputs or Assertions**: See leaf issues
- **Authoritative Enforcement Point**: See leaf issues
- **Independent Verification**: See leaf issues
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: N/A
- **Mapping Basis**: Workstream parent; mappings are made per leaf issue.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given every leaf issue below is closed, when the workstream's source requirements are checked against the coverage table, then each is COVERED by a closed leaf issue.
2. **AC-02 — Boundary or failure behavior**: Given a leaf issue is reopened or fails review, when the workstream is reviewed, then the workstream stays open.
3. **AC-03 — Prohibited behavior**: Given the workstream, when it is closed, then no source requirement has been moved to an issue outside this epic without a recorded decision.

## Failure Behavior

- **On Invalid Input**: Per leaf issue
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication (SECURITY.md SEC-AUTHN-1 to -3) is decomposed in the step 9 re-run
- **On Authorization Failure**: N/A
- **On Security-Decision Failure**: Per leaf issue
- **On External Dependency Failure**: N/A
- **On System Error**: Per leaf issue
- **Logging / Audit**: Per leaf issue
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: Per leaf issue
- **Integration Tests**: Per leaf issue
- **Security Tests**: Per leaf issue
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 to AC-03 → workstream close checklist
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Per leaf issue

## Dependencies

- **Upstream Requirements**: REQ-EPIC-001 ({{ISSUE_URL:REQ-EPIC-001}})
- **Downstream Requirements**: REQ-CONTENT-010 ({{ISSUE_URL:REQ-CONTENT-010}}), REQ-CONTENT-020 ({{ISSUE_URL:REQ-CONTENT-020}}), REQ-CONTENT-030 ({{ISSUE_URL:REQ-CONTENT-030}}), REQ-CONTENT-040 ({{ISSUE_URL:REQ-CONTENT-040}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A

## Implementation Notes

- **Constraints**: DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Implementation work in the workstream issue itself; it only groups leaves
- **Implementation Guidance**: Work the leaves in ISSUE_PLAN.md Topological Creation Order
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request.
- **Required Human Review**: Per leaf issue
- **Open Decisions**: None (step 10: Hugo; Markdown with YAML front matter in `dalini/nina-content`). Exhibition entries and News items (FR-4, PQ-11 answered) are decomposed in the step 9 re-run.

## Sub-issues

- [ ] REQ-CONTENT-010 {{ISSUE_URL:REQ-CONTENT-010}}
- [ ] REQ-CONTENT-020 {{ISSUE_URL:REQ-CONTENT-020}}
- [ ] REQ-CONTENT-030 {{ISSUE_URL:REQ-CONTENT-030}}
- [ ] REQ-CONTENT-040 {{ISSUE_URL:REQ-CONTENT-040}}
