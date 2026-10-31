## Metadata

- **ID**: REQ-EDIT-000
- **Title**: Editing Interface (Sveltia CMS)
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Functional
- **Source / Parent**: REQUIREMENTS.md FR-8.6, FR-10.1 to FR-10.3, FR-10.6 to FR-10.8; SECURITY.md SEC-CICD-2, SEC-INPUT-1, SEC-INPUT-2, SEC-INPUT-5, SEC-OUT-1, SEC-OUT-4; ARCHITECTURE.md Editing Interface, DR-9; parent {{ISSUE_URL:REQ-EPIC-001}}

## Requirement

- **Statement**: The leaf issues of this workstream MUST let editors manage all content types, uploads, Instagram settings and history in a pinned Sveltia CMS on `edit.nina-laaf.de`, with the required-field and upload checks, safe display, and the publish status shown to Nina.
- **Rationale**: Groups leaf issues that share components, rules and dependencies so they can be planned and reviewed together.
- **Assumptions**: None
- **Out of Scope**: Implementation work in this workstream issue itself; behavior outside the listed leaves. Sign-in through the OAuth helper (REQ-AUTHN-000). How the Editing Interface files reach the VPS (decided in step 10 a third time, PQ-54; drafted by the next step 9 run); how an Exhibition entry's slug is filled in (decided in step 10 a third time, PQ-57; drafted by the next step 9 run).
- **Design Traceability**: N/A
- **Architecture Traceability**: Editing Interface; Content Store; DR-3, DR-8, DR-9
- **Security Traceability**: SEC-CICD-2, SEC-INPUT-1, SEC-INPUT-2, SEC-INPUT-5, SEC-OUT-1, SEC-OUT-4

## Scope

- **Applies To**: Multiple
- **Components**: Editing Interface, Content Store
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
- **Trust Boundary**: TB-2, TB-3
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
- **Downstream Requirements**: REQ-EDIT-010 ({{ISSUE_URL:REQ-EDIT-010}}), REQ-EDIT-020 ({{ISSUE_URL:REQ-EDIT-020}}), REQ-EDIT-030 ({{ISSUE_URL:REQ-EDIT-030}}), REQ-EDIT-040 ({{ISSUE_URL:REQ-EDIT-040}}), REQ-EDIT-050 ({{ISSUE_URL:REQ-EDIT-050}}), REQ-EDIT-060 ({{ISSUE_URL:REQ-EDIT-060}}), REQ-EDIT-070 ({{ISSUE_URL:REQ-EDIT-070}}), REQ-EDIT-080 ({{ISSUE_URL:REQ-EDIT-080}}), REQ-EDIT-090 ({{ISSUE_URL:REQ-EDIT-090}})
- **External Dependencies**: Sveltia CMS; GitHub API
- **Dependency Assumptions**: See the leaf issues
- **Failure Impact**: See the leaf issues

## Implementation Notes

- **Constraints**: DEP-1 to DEP-8 apply to every dependency added; ARCHITECTURE.md DR-1 to DR-9
- **Prohibited Approaches**: Implementation work in the workstream issue itself; it only groups leaves
- **Implementation Guidance**: Work the leaves in ISSUE_PLAN.md Topological Creation Order
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement each leaf as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request.
- **Required Human Review**: Per leaf issue
- **Open Decisions**: None. Answered in step 10 a third time (`.planning/OPEN_QUESTIONS.md`): PQ-54: Ives installs the Editing Interface files by hand from a tagged, verified release of `dalini/nina-site`; root-owned, read-only to nginx and the helper; no deploy key writes them (SEC-CICD-2); PQ-57: the slug is generated from title and start year at first save (e.g. `faltungen-2025`) and never changes; duplicate slugs fail the publish, naming both entries (FR-4.7, SEC-INPUT-6); PQ-63: German, informal: "Deine Änderungen sind noch nicht online." and "Bitte prüfe: <Eintrag>, Feld <Feld>."; a strip above the CMS on `surface` with an `error` left border (FR-10.8, DESIGN.md Publish status banner). Parts that were excluded as blocked scope are drafted by the next step 9 run.

## Sub-issues

- [ ] REQ-EDIT-010 {{ISSUE_URL:REQ-EDIT-010}}
- [ ] REQ-EDIT-020 {{ISSUE_URL:REQ-EDIT-020}}
- [ ] REQ-EDIT-030 {{ISSUE_URL:REQ-EDIT-030}}
- [ ] REQ-EDIT-040 {{ISSUE_URL:REQ-EDIT-040}}
- [ ] REQ-EDIT-050 {{ISSUE_URL:REQ-EDIT-050}}
- [ ] REQ-EDIT-060 {{ISSUE_URL:REQ-EDIT-060}}
- [ ] REQ-EDIT-070 {{ISSUE_URL:REQ-EDIT-070}}
- [ ] REQ-EDIT-080 {{ISSUE_URL:REQ-EDIT-080}}
- [ ] REQ-EDIT-090 {{ISSUE_URL:REQ-EDIT-090}}
