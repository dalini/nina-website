## Metadata

- **ID**: REQ-STORE-050
- **Title**: Mirror dalini/nina-content weekly to the VPS, keep 30 days outside the served directories, and test a restore yearly
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Reliability
- **Source / Parent**: SECURITY.md SEC-AVAIL-1 (weekly mirror to the VPS, 30 days, yearly restore test, outside served directories, maintainer only), SEC-AUTHZ-4 (read-only credential), SEC-SECRET-1; ARCHITECTURE.md Content Store (Backup); T-16, T-65; parent {{ISSUE_URL:REQ-STORE-000}}

## Requirement

- **Statement**: A job under the backup account MUST mirror `dalini/nina-content` (history and media originals) to the VPS every week with a read-only credential and keep each mirror for 30 days; the mirrors MUST be stored outside every directory nginx serves and MUST NOT be readable by the web server, deploy or OAuth helper accounts (only by the maintainer and the backup account that writes them); and Ives MUST test a restore into an empty store once a year.
- **Rationale**: Content and originals must survive the loss of the GitHub account or provider (T-16); the copy on the internet-facing VPS must not become downloadable (T-65).
- **Assumptions**: None
- **Out of Scope**: Purging content from the backups on a legal request (REQ-STORE-060); an alert for a failed run (SEC-AVAIL-1 requires none). Residual risk (SEC-AVAIL-1): the backup is not independent of the VPS.
- **Design Traceability**: N/A
- **Architecture Traceability**: Content Store (Backup: weekly mirror to the VPS); Static Hosting (holds the backup); DF-11
- **Security Traceability**: SEC-AVAIL-1, SEC-AUTHZ-4, SEC-SECRET-1

## Scope

- **Applies To**: Server-Side Application
- **Components**: Content Store, Static Hosting
- **Interfaces / Operations**: Weekly cron job of the backup account; backup directory; yearly restore runbook in `docs/ops/`
- **Actors**: Backup account; maintainer (Ives); GitHub
- **Preconditions**: REQ-HOST-030, REQ-STORE-010
- **Data Classification**: Confidential
- **Personal or Regulated Data**: Personal Data (editor identities in the history; location metadata in originals)
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Availability, Confidentiality
- **Control Layers**: Availability, Data Protection
- **Threat References**: STRIDE Denial of Service and Information Disclosure: SECURITY.md Threat Model v3 T-16, T-65; CWE-530
- **Abuse / Misuse Case**: The mirror lands in a served directory and is downloaded with unpublished content and original metadata
- **Trust Boundary**: DF-11 (Content Store → VPS); account separation on the VPS
- **Untrusted Inputs or Assertions**: Data fetched from GitHub
- **Authoritative Enforcement Point**: File permissions and nginx roots on the VPS
- **Independent Verification**: Checked from outside the account or process under test, against the live host or settings
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: NIST SP 800-218 SSDF 1.1 (`REF-SSDF`)
- **Mapping Basis**: Other: `REF-SSDF` as cited in SECURITY.md SEC-AVAIL-1; no practice IDs verified.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given the weekly job runs, when it finishes, then a mirror of `dalini/nina-content` with its full history and media originals exists on the VPS, made with the backup account's read-only credential, and mirrors older than 30 days are deleted.
2. **AC-02 — Boundary or failure behavior**: Given GitHub is unreachable or the credential is revoked, when the job runs, then the existing mirrors are kept unchanged and the job exits non-zero and logs the failure.
3. **AC-03 — Boundary or failure behavior**: Given the yearly restore test, when a mirror is restored into an empty test content repository and the Site Build runs on it, then it produces the same site as the live content of that version.
4. **AC-04 — Prohibited behavior**: Given the backup path, when it is checked against every nginx root and with the web server's, deploy and OAuth helper accounts, then it is outside every served directory and none of these accounts can read it.

## Failure Behavior

- **On Invalid Input**: N/A
- **On Authentication Failure**: A rejected credential leaves the existing mirrors unchanged and logs the failure
- **On Authorization Failure**: Denied by the operating system or the platform; nothing changes
- **On Security-Decision Failure**: Deny by default
- **On External Dependency Failure**: GitHub unreachable: existing mirrors are kept; the next weekly run tries again
- **On System Error**: The job stops with a non-zero exit status, changes nothing it has not finished, and leaves the previous state in place
- **Logging / Audit**: Each run logs time, result and the mirrored commit; yearly restore test recorded in `docs/ops/`
- **Alerting**: N/A: SEC-AVAIL-1 requires no alert; failed runs are logged and the yearly restore test shows a missing mirror

## Test Strategy

- **Unit Tests**: Retention selection (older than 30 days)
- **Integration Tests**: Mirror of a test content repository on the test VPS; restore into an empty repository and build
- **Security Tests**: Read attempts by the other accounts; HTTP requests for the backup path on all three hosts
- **Compliance Tests / Evidence**: Yearly restore test record in `docs/ops/`
- **Acceptance-Criteria Traceability**: AC-01 → `test:STORE-050/AC-01`; AC-02 → `test:STORE-050/AC-02`; AC-03 → `test:STORE-050/AC-03`; AC-04 → `test:STORE-050/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test VPS (or the VPS before go-live) with the accounts and file layout of REQ-HOST-030, and a test content repository (not `dalini/nina-content`)

## Dependencies

- **Upstream Requirements**: REQ-HOST-030 ({{ISSUE_URL:REQ-HOST-030}}), REQ-STORE-010 ({{ISSUE_URL:REQ-STORE-010}})
- **Downstream Requirements**: REQ-STORE-060 ({{ISSUE_URL:REQ-STORE-060}})
- **External Dependencies**: GitHub (read access to `dalini/nina-content`)
- **Dependency Assumptions**: GitHub allows a read-only credential to clone the full history
- **Failure Impact**: Missing mirrors reduce recovery options; the live site is unaffected

## Implementation Notes

- **Constraints**: Configuration and scripts kept in `dalini/nina-site`; runbooks and review records in `docs/ops/` of `dalini/nina-website`, with no secrets and no personal data beyond the editors' names (SEC-SECRET-1); DEP-1 to DEP-8 apply to every package added
- **Prohibited Approaches**: Settings applied by hand without being kept in the reviewed configuration or runbook; secrets in scripts, command lines, logs or `docs/ops/`
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: A cron mirror with retention and permissions, plus a yearly restore drill; values and locations are fixed by SEC-AVAIL-1.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
