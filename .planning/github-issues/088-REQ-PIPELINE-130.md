## Metadata

- **ID**: REQ-PIPELINE-130
- **Title**: Check main of the Content Store every minute from the VPS and start the build through workflow_dispatch when it changed
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Operational
- **Source / Parent**: REQUIREMENTS.md FR-10.4 (15 minutes); SECURITY.md SEC-CICD-4 (VPS side, token scope, inputs), SEC-SECRET-1, SEC-CICD-8 (rotation), SEC-AUTHZ-4; ARCHITECTURE.md Site Build (When it runs), Static Hosting; T-22, T-55, T-58; parent {{ISSUE_URL:REQ-PIPELINE-000}}

## Requirement

- **Statement**: A cron job under the content check's account on the VPS MUST check `main` of `dalini/nina-content` every minute with `git ls-remote` and a read-only credential, and when the commit differs from the last one it started a build for, MUST start the build workflow of `dalini/nina-site` through `workflow_dispatch` with a fine-grained token limited to Actions: write on `dalini/nina-site`; it MUST send no dispatch inputs, or only a validated commit ID used as data; and it MUST detect and record for the alert to Ives when it cannot read the Content Store or cannot start a build.
- **Rationale**: This check meets the 15-minute limit of FR-10.4 without using Actions minutes (ARCHITECTURE.md); a silent failure would break FR-10.4 unnoticed (T-58).
- **Assumptions**: None
- **Out of Scope**: The workflow's triggers and concurrency group (REQ-PIPELINE-080); delivering the alert to Ives by email (blocked: PQ-56); the account and secret files (REQ-HOST-030).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build (When it runs: VPS content check, `workflow_dispatch`); Static Hosting (runs the content check); DF-4
- **Security Traceability**: SEC-CICD-4, SEC-SECRET-1, SEC-CICD-8, SEC-AUTHZ-4

## Scope

- **Applies To**: Background Processing
- **Components**: Static Hosting (VPS), Site Build pipeline, Content Store
- **Interfaces / Operations**: Cron job of the content check's account; `git ls-remote`; GitHub `workflow_dispatch` API
- **Actors**: Content check account; GitHub; maintainer (Ives)
- **Preconditions**: REQ-PIPELINE-080, REQ-HOST-030
- **Data Classification**: Restricted
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Availability, Integrity
- **Control Layers**: Input Validation, Availability
- **Threat References**: STRIDE Spoofing, Tampering and Denial of Service: SECURITY.md Threat Model v3 T-22, T-55, T-56, T-58; CWE-78
- **Abuse / Misuse Case**: A leaked trigger token starts builds repeatedly; a crafted ref name or input tries to inject a command
- **Trust Boundary**: DF-4 (publish trigger); TB-3, TB-4
- **Untrusted Inputs or Assertions**: Output of `git ls-remote` (ref names, commit IDs); GitHub API responses
- **Authoritative Enforcement Point**: The content check (commit ID validation, no inputs); GitHub token scope
- **Independent Verification**: Dispatch attempt without the token is rejected by GitHub; review of the token's permissions
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP CI/CD Security Cheat Sheet (`REF-CICD`)
- **Mapping Basis**: Other: `REF-CICD` as cited in SECURITY.md SEC-CICD-4.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given Nina saves a change, when the next minute's check runs, then it sees a new commit on `main` with `git ls-remote`, calls `workflow_dispatch` of the build workflow once, records that commit, and the change is live within 15 minutes of saving.
2. **AC-02 — Boundary or failure behavior**: Given `main` has not changed since the last started build, when the check runs, then it starts no build.
3. **AC-03 — Boundary or failure behavior**: Given the read credential is revoked or the dispatch call is rejected, when the check runs, then it records the failure for the alert path, keeps the last recorded commit, and tries again on the next run.
4. **AC-04 — Prohibited behavior**: Given the dispatch call, when it is reviewed, then it sends no inputs, or only the validated commit ID used as data, and no value read from the Content Store reaches a shell command.
5. **AC-05 — Additional criterion**: Given the trigger token, when its scope is reviewed, then it is a fine-grained token limited to Actions: write on `dalini/nina-site`, stored as in REQ-HOST-030; the runbook covers rotating it after an unexplained run or usage alert (SEC-CICD-8).

## Failure Behavior

- **On Invalid Input**: A commit ID that is not 40 hexadecimal characters is ignored and recorded as a failure; no build starts
- **On Authentication Failure**: A rejected credential or token is recorded for the alert path; the last recorded commit stays
- **On Authorization Failure**: Denied by the operating system or the platform; nothing changes
- **On Security-Decision Failure**: Deny by default
- **On External Dependency Failure**: GitHub unreachable: no build starts; the next run tries again; the nightly build still runs
- **On System Error**: The job stops with a non-zero exit status, changes nothing it has not finished, and leaves the previous state in place
- **Logging / Audit**: Each started build is logged with the commit ID and time, without token values; the build records its trigger (SEC-AUDIT-2)
- **Alerting**: Failures are recorded for the alert path; delivering the email is blocked scope (PQ-56)

## Test Strategy

- **Unit Tests**: Change detection; commit ID validation; state file handling
- **Integration Tests**: Against a test content repository and a test workflow: save → dispatch → build within 15 minutes
- **Security Tests**: Dispatch without the token; ref names with shell metacharacters; revoked credential
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:PIPELINE-130/AC-01`; AC-02 → `test:PIPELINE-130/AC-02`; AC-03 → `test:PIPELINE-130/AC-03`; AC-04 → `test:PIPELINE-130/AC-04`; AC-05 → `test:PIPELINE-130/AC-05` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test VPS (or the VPS before go-live) with the accounts and file layout of REQ-HOST-030, and a test content repository (not `dalini/nina-content`)

## Dependencies

- **Upstream Requirements**: REQ-PIPELINE-080 ({{ISSUE_URL:REQ-PIPELINE-080}}), REQ-HOST-030 ({{ISSUE_URL:REQ-HOST-030}})
- **Downstream Requirements**: None
- **External Dependencies**: GitHub (`git ls-remote`, Actions API)
- **Dependency Assumptions**: GitHub accepts `workflow_dispatch` with a fine-grained token limited to Actions: write
- **Failure Impact**: Changes wait for the nightly build until the check works again

## Implementation Notes

- **Constraints**: Configuration and scripts kept in `dalini/nina-site`; runbooks and review records in `docs/ops/` of `dalini/nina-website`, with no secrets and no personal data beyond the editors' names (SEC-SECRET-1); DEP-1 to DEP-8 apply to every package added
- **Prohibited Approaches**: Settings applied by hand without being kept in the reviewed configuration or runbook; secrets in scripts, command lines, logs or `docs/ops/`
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: A small job that holds a trigger token, must never pass content into a command, and is the only path that meets the 15-minute limit.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: PQ-56 (blocking) for the excluded alert delivery.
