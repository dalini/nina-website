## Metadata

- **ID**: REQ-PIPELINE-120
- **Title**: Write the publish status file with only the content version, the result and the failing item and field
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-CICD-9, SEC-CICD-1, `REF-SECRETS`; REQUIREMENTS.md FR-10.8; ARCHITECTURE.md Site Build (Outputs), DR-1; parent {{ISSUE_URL:REQ-PIPELINE-000}}

## Requirement

- **Statement**: Every production run MUST write the publish status file (DF-9) through the deploy channel, containing only the Content Store version, the result (success or failure) and, for a failure caused by content, the failing item's identifier and field name; it MUST NOT contain content values, build log output or secrets. The file is public: nginx serves it from `edit.nina-laaf.de` without an access check (SEC-CICD-9), and the deploy account writes nothing else on that host (SEC-CICD-2).
- **Rationale**: The status lets the Editing Interface tell Nina whether her change is live (FR-10.8) without leaking unpublished content (T-59).
- **Assumptions**: None
- **Out of Scope**: Showing the status in the Editing Interface (REQ-EDIT-090).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build (publish status file on `edit.nina-laaf.de`); DR-1 (status carries no content)
- **Security Traceability**: SEC-CICD-9 (content of the file)

## Scope

- **Applies To**: Background Processing
- **Components**: Site Build pipeline (GitHub Actions in `dalini/nina-site`), Static Hosting
- **Interfaces / Operations**: Status writer in the deploy job; status file path that the deploy account may write (SEC-CICD-1)
- **Actors**: build/deploy identity; maintainer (Ives)
- **Preconditions**: REQ-PIPELINE-010, REQ-CONTENT-030
- **Data Classification**: Internal
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Confidentiality, Integrity
- **Control Layers**: Data Protection
- **Threat References**: STRIDE Information Disclosure / Tampering: SECURITY.md Threat Model v3 T-59, T-60, T-24
- **Abuse / Misuse Case**: Error text from a failing item reveals an unpublished work's title through the status file
- **Trust Boundary**: TB-4 (Content Store ↔ Site Build ↔ Static Hosting)
- **Untrusted Inputs or Assertions**: Failure details produced from content
- **Authoritative Enforcement Point**: Site Build pipeline configuration in `dalini/nina-site` (maintainer-only, DR-8)
- **Independent Verification**: Configuration review by Ives and a test run that tries the prohibited path
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP CI/CD Security Cheat Sheet (`REF-CICD`); NIST SP 800-218 SSDF 1.1 (`REF-SSDF`)
- **Mapping Basis**: Other: `REF-CICD` and `REF-SSDF` as cited in SECURITY.md; no practice IDs verified.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given a successful run, when it ends, then the status file holds the Content Store version and `success` and nothing else.
2. **AC-02 — Boundary or failure behavior**: Given a run failing on a work's missing year, when it ends, then the status file holds the version, `failure`, the work's identifier and `year`.
3. **AC-03 — Prohibited behavior**: Given a failing item whose fields contain marker text, when the status file is written, then it contains none of the marker text, no log lines and no secrets.
4. **AC-04 — Additional criterion**: Given an unauthenticated request for the status file on `edit.nina-laaf.de`, when it is made, then the file is served and holds only the listed fields; and given the deploy account tries to write another file on that host, then it is denied.

## Failure Behavior

- **On Invalid Input**: The run fails before any deploy step; nothing is published
- **On Authentication Failure**: N/A: the pipeline authenticates with its own credentials only
- **On Authorization Failure**: A step without the needed permission fails; the run stops and publishes nothing
- **On Security-Decision Failure**: Deny by default: a missing or failed check stops the run before deploy
- **On External Dependency Failure**: GitHub Actions unavailable: no build runs and the previous site stays online
- **On System Error**: The run fails and publishes nothing; the previous site stays online
- **Logging / Audit**: The run log records steps and results; secrets are masked and never printed (SEC-SECRET-1)
- **Alerting**: A failed run emails Ives (REQ-PIPELINE-110)

## Test Strategy

- **Unit Tests**: Status writer with a fixed field set
- **Integration Tests**: Successful and failing runs against the test host
- **Security Tests**: Marker-text fixture (SEC-CICD-9 Verification)
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:PIPELINE-120/AC-01`; AC-02 → `test:PIPELINE-120/AC-02`; AC-03 → `test:PIPELINE-120/AC-03`; AC-04 → `test:PIPELINE-120/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test copy of the workflow in a test repository, a test content repository and a test target host with test keys; never the production keys

## Dependencies

- **Upstream Requirements**: REQ-PIPELINE-010 ({{ISSUE_URL:REQ-PIPELINE-010}}), REQ-CONTENT-030 ({{ISSUE_URL:REQ-CONTENT-030}})
- **Downstream Requirements**: REQ-EDIT-090 ({{ISSUE_URL:REQ-EDIT-090}})
- **External Dependencies**: GitHub Actions
- **Dependency Assumptions**: GitHub Actions runs workflows as configured
- **Failure Impact**: A pipeline failure delays publishing; the previous site stays online

## Implementation Notes

- **Constraints**: GitHub Actions in `dalini/nina-site`, Hugo, rsync over SSH (ARCHITECTURE.md Deployment model); secrets come from the `production` environment of `dalini/nina-site`, limited to `main` (REQ-PIPELINE-050); DEP-1 to DEP-8 apply
- **Prohibited Approaches**: Printing secrets; giving content-processing steps access to deploy keys or the Instagram token (SEC-CICD-6)
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: A small writer with a fixed schema; the open access question is excluded.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
