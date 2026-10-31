## Metadata

- **ID**: REQ-PIPELINE-070
- **Title**: Record what each production deploy was built from and restore the previous version without a content change
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 draft, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Operational
- **Source / Parent**: SECURITY.md SEC-AUDIT-2; parent {{ISSUE_URL:REQ-PIPELINE-000}}

## Requirement

- **Statement**: Every production deploy MUST record the Content Store version it was built from and what triggered it, and the previously deployed version MUST be restorable without a new content change.
- **Rationale**: After a defacement or a bad publish, the live version must be identifiable and quickly replaced (SECURITY.md T-23, T-27).
- **Assumptions**: None (the Content Store is the private Git repository `dalini/nina-content`, ARCHITECTURE.md)
- **Out of Scope**: Change history of content (SEC-AUDIT-1) and backups (SEC-AVAIL-1: weekly mirror to the VPS, 30 days): decomposed in the step 9 re-run.
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build, Static Hosting (TB-4, DF-6)
- **Security Traceability**: SEC-AUDIT-2

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build, Static Hosting
- **Interfaces / Operations**: Deploy records; restore procedure
- **Actors**: Build/deploy identity; maintainer (Ives); Site Build
- **Preconditions**: REQ-PIPELINE-010
- **Data Classification**: Restricted (deploy credentials); Public (output)
- **Personal or Regulated Data**: Personal Data (identity of the person whose change triggered a deploy, if recorded)
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Accountability, Availability
- **Control Layers**: Logging and Monitoring, Availability
- **Threat References**: STRIDE Repudiation: SECURITY.md Threat Model v1 T-23, T-27
- **Abuse / Misuse Case**: An attacker deploys altered files and nobody can tell which version is live or roll back.
- **Trust Boundary**: TB-4 (Content Store ↔ Site Build ↔ Static Hosting pipeline)
- **Untrusted Inputs or Assertions**: Trigger metadata supplied to the pipeline
- **Authoritative Enforcement Point**: CI/CD platform configuration and Static Hosting
- **Independent Verification**: Branch protection and CI permissions are enforced by the platform, not by the build script
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: NIST SP 800-218 SSDF 1.1 (`REF-SSDF`)
- **Mapping Basis**: Other: `REF-SSDF` as cited in SECURITY.md.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given a production deploy, when its record is read, then it shows the Content Store version, the trigger and the time.
2. **AC-02 — Expected behavior**: Given two consecutive deploys, when the restore procedure is run, then the previous version is live again without any new commit to the Content Store.
3. **AC-03 — Boundary or failure behavior**: Given a deploy whose record cannot be written, when the pipeline runs, then the deploy is reported as failed.
4. **AC-04 — Prohibited behavior**: Given deploy records, when they are inspected, then they contain no secrets.

## Failure Behavior

- **On Invalid Input**: N/A
- **On Authentication Failure**: N/A: the public site and the Site Build have no sign-in; editor authentication (SECURITY.md SEC-AUTHN-1 to -3) is decomposed in the step 9 re-run
- **On Authorization Failure**: Deploy attempts by any other identity or branch are rejected by the platform; nothing is deployed
- **On Security-Decision Failure**: Missing provenance blocks the deploy
- **On External Dependency Failure**: N/A
- **On System Error**: The pipeline stops; the previously deployed site stays online
- **Logging / Audit**: Deploy record: Content Store version, trigger, time, result; no secrets
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: N/A
- **Integration Tests**: Two deploys followed by a restore drill
- **Security Tests**: Inspection of deploy records for secrets
- **Compliance Tests / Evidence**: Restore drill result kept as evidence
- **Acceptance-Criteria Traceability**: AC-01 → `test:PIPELINE-070/AC-01`; AC-02 → `test:PIPELINE-070/AC-02`; AC-03 → `test:PIPELINE-070/AC-03`; AC-04 → `test:PIPELINE-070/AC-04`
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: GitHub Actions in `dalini/nina-site` and the VPS with nginx (ARCHITECTURE.md) with a test branch and a test deploy target

## Dependencies

- **Upstream Requirements**: REQ-PIPELINE-010 ({{ISSUE_URL:REQ-PIPELINE-010}})
- **Downstream Requirements**: None
- **External Dependencies**: GitHub Actions in `dalini/nina-site`; VPS with nginx (ARCHITECTURE.md)
- **Dependency Assumptions**: The platform enforces branch protection, job permissions and secret scoping as configured
- **Failure Impact**: A platform outage stops publishing; the live site stays as it is (ARCHITECTURE.md DR-1)

## Implementation Notes

- **Constraints**: DEP-1 to DEP-8 apply to every dependency added
- **Prohibited Approaches**: Restoring by re-committing old content (that is a content change)
- **Implementation Guidance**: Platform deploy history may already satisfy the record if it carries the Content Store version.
- **AI Development Guidance**: Follow CLAUDE.md and the spec file that owns each domain; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`), because: Deploy record and restore drill using platform features.
- **Required Human Review**: Security review; operational review of the restore procedure
- **Open Decisions**: None (step 10: GitHub Actions and the VPS).
