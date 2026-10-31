## Metadata

- **ID**: REQ-PIPELINE-000
- **Title**: Secrets and build/deploy pipeline
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Repository maintainer (dalini); the maintainer role is still open in SECURITY.md SQ-10
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-CICD-1, SEC-CICD-2, SEC-CICD-3, SEC-CICD-5, SEC-SECRET-1, SEC-SECRET-2, SEC-AUDIT-2, SEC-INPUT-3; REQUIREMENTS.md FR-10.4; parent {{ISSUE_URL:REQ-EPIC-001}}

## Requirement

- **Statement**: The leaf issues of this workstream MUST build and deploy the site from the Content Store's main branch with the pipeline controls of SEC-CICD-1, -2, -3, -5, SEC-SECRET-1, -2 and SEC-AUDIT-2.
- **Rationale**: Groups leaf issues that share components and dependencies so they can be planned and reviewed together.
- **Assumptions**: None
- **Out of Scope**: Behavior outside the listed leaf issues; see ISSUE_PLAN.md Blocked Scope for related blocked requirements.
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build, Static Hosting (TB-4); DR-1, DR-8
- **Security Traceability**: Listed rules; T-19 to T-23, T-27, T-29

## Scope

- **Applies To**: Multiple
- **Components**: Site Build, Static Hosting
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

- **OWASP ASVS 5.0.0**: TO BE DECIDED: SECURITY.md cites ASVS 5.0.0 but uses no ASVS identifiers until they are verified; assurance level open in SECURITY.md SQ-9
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: TO BE DECIDED: not assessed
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
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication is blocked scope (ISSUE_PLAN.md PQ-2)
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
- **Coverage Target**: Project coverage target TO BE DECIDED (build and test commands are TO BE DECIDED in CLAUDE.md). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: Per leaf issue

## Dependencies

- **Upstream Requirements**: REQ-EPIC-001 ({{ISSUE_URL:REQ-EPIC-001}})
- **Downstream Requirements**: REQ-PIPELINE-010 ({{ISSUE_URL:REQ-PIPELINE-010}}), REQ-PIPELINE-020 ({{ISSUE_URL:REQ-PIPELINE-020}}), REQ-PIPELINE-030 ({{ISSUE_URL:REQ-PIPELINE-030}}), REQ-PIPELINE-040 ({{ISSUE_URL:REQ-PIPELINE-040}}), REQ-PIPELINE-050 ({{ISSUE_URL:REQ-PIPELINE-050}}), REQ-PIPELINE-060 ({{ISSUE_URL:REQ-PIPELINE-060}}), REQ-PIPELINE-070 ({{ISSUE_URL:REQ-PIPELINE-070}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: N/A

## Implementation Notes

- **Constraints**: DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Implementation work in the workstream issue itself; it only groups leaves
- **Implementation Guidance**: Work the leaves in ISSUE_PLAN.md Topological Creation Order
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request.
- **Required Human Review**: Per leaf issue
- **Open Decisions**: Blocking, excluded: PQ-1 (publish trigger from the Editing Interface, SEC-CICD-4), PQ-4 (maintainer access, SEC-AUTHZ-3). Non-blocking: PQ-17, PQ-18.

## Sub-issues

- [ ] REQ-PIPELINE-010 {{ISSUE_URL:REQ-PIPELINE-010}}
- [ ] REQ-PIPELINE-020 {{ISSUE_URL:REQ-PIPELINE-020}}
- [ ] REQ-PIPELINE-030 {{ISSUE_URL:REQ-PIPELINE-030}}
- [ ] REQ-PIPELINE-040 {{ISSUE_URL:REQ-PIPELINE-040}}
- [ ] REQ-PIPELINE-050 {{ISSUE_URL:REQ-PIPELINE-050}}
- [ ] REQ-PIPELINE-060 {{ISSUE_URL:REQ-PIPELINE-060}}
- [ ] REQ-PIPELINE-070 {{ISSUE_URL:REQ-PIPELINE-070}}
