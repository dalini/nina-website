## Metadata

- **ID**: REQ-EXHIBITIONS-000
- **Title**: Exhibitions and News
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-4.1 to FR-4.7; DESIGN.md Components (Exhibition entry page, Interface labels); parent {{ISSUE_URL:REQ-EPIC-001}}

## Requirement

- **Statement**: The leaf issues of this workstream MUST list Exhibition entries with their facts, grouped as upcoming, current and past, give each entry its own page with its linked works, description and installation images, and list News items in their own section.
- **Rationale**: Groups leaf issues that share components, rules and dependencies so they can be planned and reviewed together.
- **Assumptions**: None
- **Out of Scope**: Implementation work in this workstream issue itself; behavior outside the listed leaves. Duplicate slugs and how the slug is filled in (decided in step 10 a third time, PQ-57; drafted by the next step 9 run).
- **Design Traceability**: DESIGN.md Components (Exhibition entry page, Image with credit, Interface labels), Layout and Spacing
- **Architecture Traceability**: Site Build; Public Site
- **Security Traceability**: N/A

## Scope

- **Applies To**: Multiple
- **Components**: Site Build, Public Site
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
- **Downstream Requirements**: REQ-EXHIBITIONS-010 ({{ISSUE_URL:REQ-EXHIBITIONS-010}}), REQ-EXHIBITIONS-030 ({{ISSUE_URL:REQ-EXHIBITIONS-030}}), REQ-EXHIBITIONS-020 ({{ISSUE_URL:REQ-EXHIBITIONS-020}}), REQ-NEWS-010 ({{ISSUE_URL:REQ-NEWS-010}})
- **External Dependencies**: See the leaf issues
- **Dependency Assumptions**: See the leaf issues
- **Failure Impact**: See the leaf issues

## Implementation Notes

- **Constraints**: DEP-1 to DEP-8 apply to every dependency added; ARCHITECTURE.md DR-1 to DR-9
- **Prohibited Approaches**: Implementation work in the workstream issue itself; it only groups leaves
- **Implementation Guidance**: Work the leaves in ISSUE_PLAN.md Topological Creation Order
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement each leaf as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request.
- **Required Human Review**: Per leaf issue
- **Open Decisions**: None. Answered in step 10 a third time (`.planning/OPEN_QUESTIONS.md`): PQ-57: the slug is generated from title and start year at first save (e.g. `faltungen-2025`) and never changes; duplicate slugs fail the publish, naming both entries (FR-4.7, SEC-INPUT-6); PQ-65: numeric dates: DE `DD.MM.YYYY`, EN `YYYY-MM-DD`; ranges with both full dates and an en dash (FR-1.8). Parts that were excluded as blocked scope are drafted by the next step 9 run.

## Sub-issues

- [ ] REQ-EXHIBITIONS-010 {{ISSUE_URL:REQ-EXHIBITIONS-010}}
- [ ] REQ-EXHIBITIONS-030 {{ISSUE_URL:REQ-EXHIBITIONS-030}}
- [ ] REQ-EXHIBITIONS-020 {{ISSUE_URL:REQ-EXHIBITIONS-020}}
- [ ] REQ-NEWS-010 {{ISSUE_URL:REQ-NEWS-010}}
