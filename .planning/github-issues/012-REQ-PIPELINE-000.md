## Metadata

- **ID**: REQ-PIPELINE-000
- **Title**: Secrets and build/deploy pipeline
- **Version**: 0.3.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-SECRET-1, SEC-SECRET-2, SEC-CICD-1 to SEC-CICD-9, SEC-AUDIT-2, SEC-AUTHN-1, SEC-AUTHN-2; REQUIREMENTS.md FR-10.4, FR-10.8, FR-10.9; ARCHITECTURE.md Site Build, Deployment model; parent {{ISSUE_URL:REQ-EPIC-001}}

## Requirement

- **Statement**: The leaf issues of this workstream MUST build and deploy production and preview with least-privilege identities and restricted deploy keys, pinned tools, secrets limited to `main` and isolated from content processing, recorded deploys, a VPS content check as the only trigger besides the nightly schedule, and failure reporting.
- **Rationale**: Groups leaf issues that share components, rules and dependencies so they can be planned and reviewed together.
- **Assumptions**: None
- **Out of Scope**: Implementation work in this workstream issue itself; behavior outside the listed leaves. The email alert of the VPS content check (decided in step 10 a third time, PQ-56; drafted by the next step 9 run).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build pipeline (GitHub Actions in `dalini/nina-site`); Static Hosting
- **Security Traceability**: SEC-SECRET-1, SEC-SECRET-2, SEC-CICD-1 to SEC-CICD-9, SEC-AUDIT-2

## Scope

- **Applies To**: Multiple
- **Components**: Site Build pipeline, Static Hosting
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
- **Trust Boundary**: TB-4
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
- **Downstream Requirements**: REQ-PIPELINE-010 ({{ISSUE_URL:REQ-PIPELINE-010}}), REQ-PIPELINE-020 ({{ISSUE_URL:REQ-PIPELINE-020}}), REQ-PIPELINE-030 ({{ISSUE_URL:REQ-PIPELINE-030}}), REQ-PIPELINE-040 ({{ISSUE_URL:REQ-PIPELINE-040}}), REQ-PIPELINE-050 ({{ISSUE_URL:REQ-PIPELINE-050}}), REQ-PIPELINE-060 ({{ISSUE_URL:REQ-PIPELINE-060}}), REQ-PREVIEW-010 ({{ISSUE_URL:REQ-PREVIEW-010}}), REQ-PIPELINE-070 ({{ISSUE_URL:REQ-PIPELINE-070}}), REQ-PIPELINE-080 ({{ISSUE_URL:REQ-PIPELINE-080}}), REQ-PIPELINE-130 ({{ISSUE_URL:REQ-PIPELINE-130}}), REQ-PIPELINE-090 ({{ISSUE_URL:REQ-PIPELINE-090}}), REQ-PIPELINE-140 ({{ISSUE_URL:REQ-PIPELINE-140}}), REQ-PIPELINE-100 ({{ISSUE_URL:REQ-PIPELINE-100}}), REQ-PIPELINE-110 ({{ISSUE_URL:REQ-PIPELINE-110}}), REQ-PIPELINE-120 ({{ISSUE_URL:REQ-PIPELINE-120}})
- **External Dependencies**: GitHub Actions; VPS
- **Dependency Assumptions**: See the leaf issues
- **Failure Impact**: See the leaf issues

## Implementation Notes

- **Constraints**: DEP-1 to DEP-8 apply to every dependency added; ARCHITECTURE.md DR-1 to DR-9
- **Prohibited Approaches**: Implementation work in the workstream issue itself; it only groups leaves
- **Implementation Guidance**: Work the leaves in ISSUE_PLAN.md Topological Creation Order
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement each leaf as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request.
- **Required Human Review**: Per leaf issue
- **Open Decisions**: None. Answered in step 10 a third time (`.planning/OPEN_QUESTIONS.md`): PQ-56: VPS alerts go through a local mail relay agent that runs under its own account and alone holds the relay credential; the jobs hand mail to local `sendmail` (SEC-HTTP-5, SEC-SECRET-1); PQ-62: the preview build leaves out the consent script and its footer link (FR-9.2, SEC-PRIV-5). Parts that were excluded as blocked scope are drafted by the next step 9 run.

## Sub-issues

- [ ] REQ-PIPELINE-010 {{ISSUE_URL:REQ-PIPELINE-010}}
- [ ] REQ-PIPELINE-020 {{ISSUE_URL:REQ-PIPELINE-020}}
- [ ] REQ-PIPELINE-030 {{ISSUE_URL:REQ-PIPELINE-030}}
- [ ] REQ-PIPELINE-040 {{ISSUE_URL:REQ-PIPELINE-040}}
- [ ] REQ-PIPELINE-050 {{ISSUE_URL:REQ-PIPELINE-050}}
- [ ] REQ-PIPELINE-060 {{ISSUE_URL:REQ-PIPELINE-060}}
- [ ] REQ-PREVIEW-010 {{ISSUE_URL:REQ-PREVIEW-010}}
- [ ] REQ-PIPELINE-070 {{ISSUE_URL:REQ-PIPELINE-070}}
- [ ] REQ-PIPELINE-080 {{ISSUE_URL:REQ-PIPELINE-080}}
- [ ] REQ-PIPELINE-130 {{ISSUE_URL:REQ-PIPELINE-130}}
- [ ] REQ-PIPELINE-090 {{ISSUE_URL:REQ-PIPELINE-090}}
- [ ] REQ-PIPELINE-140 {{ISSUE_URL:REQ-PIPELINE-140}}
- [ ] REQ-PIPELINE-100 {{ISSUE_URL:REQ-PIPELINE-100}}
- [ ] REQ-PIPELINE-110 {{ISSUE_URL:REQ-PIPELINE-110}}
- [ ] REQ-PIPELINE-120 {{ISSUE_URL:REQ-PIPELINE-120}}
