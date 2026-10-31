## Metadata

- **ID**: REQ-INSTAGRAM-050
- **Title**: Keep the Instagram token in a file of the deploy account and refresh it monthly with a job under that account
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-SECRET-1 (Instagram token in a file of the deploy account), SEC-SECRET-3 (monthly refresh under the deploy account), SEC-CICD-1 (outbound HTTPS to Instagram), SEC-HOST-2; ARCHITECTURE.md Instagram Feed Integration, Static Hosting; parent {{ISSUE_URL:REQ-INSTAGRAM-000}}

## Requirement

- **Statement**: The 60-day Instagram token MUST be kept in a file on the VPS owned by the deploy account and readable only by that account, and a cron job running under the deploy account MUST refresh it monthly over verified HTTPS, keeping the previous token when a refresh fails and recording the failure for the alert to Ives; the token MUST grant only `instagram_business_basic` and MUST NOT appear in command lines, environments shared with other accounts, or logs.
- **Rationale**: The build reads the token over the deploy channel (ARCHITECTURE.md Instagram Feed Integration); one account owns it so that no root-only file has to be read by a non-root process (T-37, T-62).
- **Assumptions**: @ninalaaf is a Business or Creator account with an Instagram API with Instagram Login app (ARCHITECTURE.md)
- **Out of Scope**: Delivering the failure alert to Ives by email (decided in step 10 a third time, PQ-56; drafted by the next step 9 run); reading the token in the build (REQ-INSTAGRAM-030); which paths the deploy key can write (REQ-PIPELINE-140).
- **Design Traceability**: N/A
- **Architecture Traceability**: Instagram Feed Integration (token on the VPS, monthly refresh); Static Hosting (runs the refresh under the deploy account); DF-13
- **Security Traceability**: SEC-SECRET-1, SEC-SECRET-3, SEC-CICD-1, SEC-HOST-2

## Scope

- **Applies To**: Server-Side Application
- **Components**: Static Hosting (VPS), Instagram Feed Integration
- **Interfaces / Operations**: Monthly cron job of the deploy account; the token file; Instagram's token refresh endpoint
- **Actors**: Deploy account; Instagram (external); maintainer (Ives)
- **Preconditions**: REQ-HOST-030
- **Data Classification**: Restricted
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Confidentiality, Availability
- **Control Layers**: Data Protection, Availability
- **Threat References**: STRIDE Elevation of Privilege and Denial of Service: SECURITY.md Threat Model v3 T-37, T-62, T-81, T-39; CWE-522, CWE-732
- **Abuse / Misuse Case**: A process under another account, or a leaked deploy key, reads or replaces the token
- **Trust Boundary**: TB-5 (Instagram ↔ VPS refresh, DF-13); account separation on the VPS (SEC-HOST-2)
- **Untrusted Inputs or Assertions**: Responses of Instagram's refresh endpoint
- **Authoritative Enforcement Point**: File ownership and permissions on the VPS; Instagram's token scope
- **Independent Verification**: Checked from outside the account or process under test, against the live host or settings
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP Secrets Management Cheat Sheet (`REF-SECRETS`)
- **Mapping Basis**: Other: `REF-SECRETS` as cited in SECURITY.md SEC-SECRET-1 and SEC-SECRET-3.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given a valid 60-day token in the deploy account's token file, when the monthly refresh job runs, then it calls the refresh endpoint over HTTPS with certificate validation, writes the new token to the same file atomically, and the file stays owned by and readable only by the deploy account.
2. **AC-02 — Boundary or failure behavior**: Given the refresh endpoint is unreachable or rejects the token, when the job runs, then the old token file is left unchanged, the job exits non-zero and records the failure for the alert path.
3. **AC-03 — Boundary or failure behavior**: Given the web server's account, the OAuth helper's account, the content check's account and the backup account, when each tries to read the token file, then access is denied.
4. **AC-04 — Prohibited behavior**: Given the job's command line, environment and logs, and the token's granted permissions, when they are reviewed, then no token value appears in them, and the token grants only `instagram_business_basic`.

## Failure Behavior

- **On Invalid Input**: N/A
- **On Authentication Failure**: A rejected token is kept as is and the failure is recorded; the build keeps using the last good posts (REQ-INSTAGRAM-040)
- **On Authorization Failure**: Denied by the operating system or the platform; nothing changes
- **On Security-Decision Failure**: Deny by default
- **On External Dependency Failure**: Instagram unreachable: the token file is not changed; the next monthly run tries again; the failure is recorded
- **On System Error**: The job stops with a non-zero exit status, changes nothing it has not finished, and leaves the previous state in place
- **Logging / Audit**: The job logs time and result (success or failure, HTTP status) without the token value
- **Alerting**: A failed refresh is recorded for the alert path; delivering the email goes through the local mail relay agent (PQ-56, decided in step 10 a third time; drafted by the next step 9 run)

## Test Strategy

- **Unit Tests**: Atomic write; keeping the old token on every failure path; response parsing
- **Integration Tests**: Refresh against a mock endpoint on the test VPS; permission checks from each other account
- **Security Tests**: Search of logs, process list and environment for the test token value; certificate-validation failure against a mock with an invalid certificate
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:INSTAGRAM-050/AC-01`; AC-02 → `test:INSTAGRAM-050/AC-02`; AC-03 → `test:INSTAGRAM-050/AC-03`; AC-04 → `test:INSTAGRAM-050/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test VPS (or the VPS before go-live) with the accounts and file layout of REQ-HOST-030, and a test content repository (not `dalini/nina-content`)

## Dependencies

- **Upstream Requirements**: REQ-HOST-030 ({{ISSUE_URL:REQ-HOST-030}})
- **Downstream Requirements**: REQ-INSTAGRAM-030 ({{ISSUE_URL:REQ-INSTAGRAM-030}})
- **External Dependencies**: Instagram API with Instagram Login (token refresh)
- **Dependency Assumptions**: A long-lived token can be refreshed before its 60 days run out (ARCHITECTURE.md)
- **Failure Impact**: If refreshes keep failing the token expires; the build then reuses the last good posts (FR-8.5)

## Implementation Notes

- **Constraints**: Configuration and scripts kept in `dalini/nina-site`; runbooks and review records in `docs/ops/` of `dalini/nina-website`, with no secrets and no personal data beyond the editors' names (SEC-SECRET-1); DEP-1 to DEP-8 apply to every package added
- **Prohibited Approaches**: Settings applied by hand without being kept in the reviewed configuration or runbook; secrets in scripts, command lines, logs or `docs/ops/`
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Secret handling on the VPS across a cron job, file permissions and an outbound API call, where a mistake leaks a token or silently stops the feed.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None. Answered in step 10 a third time (`.planning/OPEN_QUESTIONS.md`): PQ-56: VPS alerts go through a local mail relay agent that runs under its own account and alone holds the relay credential; the jobs hand mail to local `sendmail` (SEC-HTTP-5, SEC-SECRET-1). Parts that were excluded as blocked scope are drafted by the next step 9 run.
