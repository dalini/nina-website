## Metadata

- **ID**: REQ-HOST-030
- **Title**: Run each VPS job and the OAuth helper under its own unprivileged account that can read only its own secret file
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-HOST-2 (unprivileged accounts, own secrets only), SEC-SECRET-1 (one secret file per account, step 10 again), SEC-AUTHN-4 (helper's own account); ARCHITECTURE.md Static Hosting; T-61, T-62; parent {{ISSUE_URL:REQ-EPIC-001}}

## Requirement

- **Statement**: Every process on the VPS that handles input from outside it (nginx workers, the OAuth helper, the content check, the Instagram token refresh, the backup, the deploy account's rsync) MUST run under an unprivileged account, never as root; the OAuth helper, the content check and the backup MUST each have their own account and the token refresh MUST run under the deploy account; and each secret file on the VPS (the OAuth helper's client secret, the build-trigger token, the read credentials of the content check and the backup, the Instagram token) MUST be owned by the one account that uses it and readable only by that account, never by root-run services, the web server or another job's account.
- **Rationale**: One flaw in any internet-facing process must not reach every secret, the backup or the other sites (T-61, T-62).
- **Assumptions**: None
- **Out of Scope**: Base hardening (REQ-HOST-020); what the deploy keys may do (REQ-PIPELINE-140); the jobs themselves (REQ-PIPELINE-130, REQ-INSTAGRAM-050, REQ-STORE-050, REQ-AUTHN-030); the credential of the email alert path (blocked: PQ-56).
- **Design Traceability**: N/A
- **Architecture Traceability**: Static Hosting (process accounts, SEC-HOST-2; each secret file belongs to the account that uses it)
- **Security Traceability**: SEC-HOST-2, SEC-SECRET-1

## Scope

- **Applies To**: Server-Side Application
- **Components**: Static Hosting
- **Interfaces / Operations**: Unix accounts and groups; file ownership and modes of the secret files; service and cron definitions
- **Actors**: Maintainer (Ives)
- **Preconditions**: REQ-HOST-020
- **Data Classification**: Restricted
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Confidentiality, Integrity
- **Control Layers**: Architecture, Authorization
- **Threat References**: STRIDE Elevation of Privilege: SECURITY.md Threat Model v3 T-61, T-62; CWE-250, CWE-732
- **Abuse / Misuse Case**: A flaw in the OAuth helper or a parser in a job reads the Instagram token or the trigger token
- **Trust Boundary**: Inside the VPS: internet-facing processes ↔ root, stored secrets, backup and logs (SECURITY.md Analysis surface)
- **Untrusted Inputs or Assertions**: Requests to the helper; data the jobs fetch from GitHub and Instagram
- **Authoritative Enforcement Point**: The VPS operating system (accounts, file permissions)
- **Independent Verification**: Checked from outside the account or process under test, against the live host or settings
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP Secrets Management Cheat Sheet (`REF-SECRETS`); NIST SP 800-218 SSDF 1.1 (`REF-SSDF`)
- **Mapping Basis**: Other: `REF-SECRETS` and `REF-SSDF` as cited in SECURITY.md SEC-SECRET-1 and SEC-HOST-2; no practice IDs verified.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given the VPS, when the process list and file permissions are checked, then the OAuth helper, the content check, the backup and the deploy account (rsync and the Instagram refresh) each run under their own non-root account, and each secret file (OAuth client secret, build-trigger token, content-check read credential, backup read credential, Instagram token) is owned by and readable only by the one account that uses it.
2. **AC-02 — Boundary or failure behavior**: Given each of these accounts and the web server's account, when it tries to read another account's secret file, then access is denied.
3. **AC-03 — Boundary or failure behavior**: Given root-run services, when their configuration is reviewed, then none of them reads one of these secret files.
4. **AC-04 — Prohibited behavior**: Given any of these accounts, when it tries to use sudo, become root or sign in over SSH with a password, then it is refused.

## Failure Behavior

- **On Invalid Input**: N/A
- **On Authentication Failure**: Password and root sign-in are refused (SEC-HOST-2)
- **On Authorization Failure**: Denied by the operating system or the platform; nothing changes
- **On Security-Decision Failure**: Deny by default
- **On External Dependency Failure**: N/A
- **On System Error**: The job stops with a non-zero exit status, changes nothing it has not finished, and leaves the previous state in place
- **Logging / Audit**: Account and permission layout recorded in the runbook in `docs/ops/` (no secrets); checked in the yearly SEC-HOST-1 review
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: N/A: configuration, no first-party code beyond a permission check script
- **Integration Tests**: Permission check script run on the test VPS as each account
- **Security Tests**: Cross-account read attempts; process list review; sudo and password sign-in attempts
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:HOST-030/AC-01`; AC-02 → `test:HOST-030/AC-02`; AC-03 → `test:HOST-030/AC-03`; AC-04 → `test:HOST-030/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test VPS (or the VPS before go-live) with the accounts and file layout of REQ-HOST-030, and a test content repository (not `dalini/nina-content`)

## Dependencies

- **Upstream Requirements**: REQ-HOST-020 ({{ISSUE_URL:REQ-HOST-020}})
- **Downstream Requirements**: REQ-STORE-050 ({{ISSUE_URL:REQ-STORE-050}}), REQ-PIPELINE-130 ({{ISSUE_URL:REQ-PIPELINE-130}}), REQ-PIPELINE-140 ({{ISSUE_URL:REQ-PIPELINE-140}}), REQ-INSTAGRAM-050 ({{ISSUE_URL:REQ-INSTAGRAM-050}}), REQ-AUTHN-030 ({{ISSUE_URL:REQ-AUTHN-030}})
- **External Dependencies**: None
- **Dependency Assumptions**: None
- **Failure Impact**: A wrong permission lets one compromised process read secrets of others

## Implementation Notes

- **Constraints**: Configuration and scripts kept in `dalini/nina-site`; runbooks and review records in `docs/ops/` of `dalini/nina-website`, with no secrets and no personal data beyond the editors' names (SEC-SECRET-1); DEP-1 to DEP-8 apply to every package added
- **Prohibited Approaches**: Settings applied by hand without being kept in the reviewed configuration or runbook; secrets in scripts, command lines, logs or `docs/ops/`
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Account and permission design on the VPS that every job and secret depends on; mistakes are silent and widen what one flaw can reach.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
