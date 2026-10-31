## Metadata

- **ID**: REQ-STORE-060
- **Title**: Document the purge of content from history and backups on a legal request, with a record that names nothing purged
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Compliance
- **Source / Parent**: SECURITY.md SEC-AUDIT-1 (exception: purge on a documented legal request, record without names), SEC-SECRET-1 (`docs/ops/`), SEC-AVAIL-1; T-14, T-83; parent {{ISSUE_URL:REQ-STORE-000}}

## Requirement

- **Statement**: On a documented legal request (withdrawn image rights, personal data entered by mistake), only Ives MAY purge content from the history of `dalini/nina-content` and from its backups, following a runbook in `docs/ops/`, and MUST keep a purge record with the date and the kind of reason that names no purged item, file or person.
- **Rationale**: Deleted content otherwise stays recoverable in history and backups (T-14); a record in the public specification repository must not republish what was removed (T-83).
- **Assumptions**: None
- **Out of Scope**: Deciding whether a request is legally valid (Nina and Ives).
- **Design Traceability**: N/A
- **Architecture Traceability**: Content Store (history); Static Hosting (backup); Repositories (`docs/ops/`)
- **Security Traceability**: SEC-AUDIT-1 (exception), SEC-SECRET-1, SEC-AVAIL-1

## Scope

- **Applies To**: Server-Side Application
- **Components**: Content Store, Static Hosting
- **Interfaces / Operations**: Purge runbook; purge record in `docs/ops/`
- **Actors**: Maintainer (Ives)
- **Preconditions**: REQ-STORE-040, REQ-STORE-050
- **Data Classification**: Confidential
- **Personal or Regulated Data**: Personal Data (possibly the data being purged)
- **Jurisdiction / Regulatory Scope**: Germany / EU: GDPR and image rights; no article mapping (SECURITY.md)

## Security Context

- **Security Objectives**: Privacy, Accountability
- **Control Layers**: Data Protection, Logging and Monitoring
- **Threat References**: STRIDE Information Disclosure: SECURITY.md Threat Model v3 T-14, T-83
- **Abuse / Misuse Case**: A purge used to hide tampering; a record that republishes the purged file name
- **Trust Boundary**: TB-3 (history of the Content Store); public `docs/ops/`
- **Untrusted Inputs or Assertions**: The purge request
- **Authoritative Enforcement Point**: Branch protection with Ives as the only bypass (REQ-STORE-040); review of the record
- **Independent Verification**: Checked from outside the account or process under test, against the live host or settings
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: N/A
- **Mapping Basis**: N/A: the rule is SEC-AUDIT-1; no external standard mapped.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given a documented legal request (e.g. withdrawn image rights), when Ives follows the runbook on a test content repository and its test mirrors, then the content is gone from the history and from every mirror, and a purge record with the date and the kind of reason is added to `docs/ops/`.
2. **AC-02 — Boundary or failure behavior**: Given Nina or the Editing Interface credential, when either tries to rewrite history, then it is rejected (REQ-STORE-040); only Ives's bypass can carry out a purge.
3. **AC-03 — Prohibited behavior**: Given any purge record, when it is reviewed, then it names no purged item, file or person and holds no content.

## Failure Behavior

- **On Invalid Input**: N/A
- **On Authentication Failure**: N/A
- **On Authorization Failure**: Denied by the operating system or the platform; nothing changes
- **On Security-Decision Failure**: Deny by default
- **On External Dependency Failure**: N/A
- **On System Error**: The job stops with a non-zero exit status, changes nothing it has not finished, and leaves the previous state in place
- **Logging / Audit**: The purge record (date, kind of reason) in `docs/ops/`
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: N/A: runbook
- **Integration Tests**: Purge drill on a test content repository and its test mirrors
- **Security Tests**: Search of the history and mirrors for the purged test file after the drill; review of the record
- **Compliance Tests / Evidence**: Purge records in `docs/ops/`
- **Acceptance-Criteria Traceability**: AC-01 → `test:STORE-060/AC-01`; AC-02 → `test:STORE-060/AC-02`; AC-03 → `test:STORE-060/AC-03` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test VPS (or the VPS before go-live) with the accounts and file layout of REQ-HOST-030, and a test content repository (not `dalini/nina-content`)

## Dependencies

- **Upstream Requirements**: REQ-STORE-040 ({{ISSUE_URL:REQ-STORE-040}}), REQ-STORE-050 ({{ISSUE_URL:REQ-STORE-050}})
- **Downstream Requirements**: None
- **External Dependencies**: GitHub
- **Dependency Assumptions**: GitHub lets the admin rewrite a protected branch with the bypass
- **Failure Impact**: Purged content survives in a mirror if one is missed

## Implementation Notes

- **Constraints**: Configuration and scripts kept in `dalini/nina-site`; runbooks and review records in `docs/ops/` of `dalini/nina-website`, with no secrets and no personal data beyond the editors' names (SEC-SECRET-1); DEP-1 to DEP-8 apply to every package added
- **Prohibited Approaches**: Settings applied by hand without being kept in the reviewed configuration or runbook; secrets in scripts, command lines, logs or `docs/ops/`
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: A runbook and a record format; the rule fixes who may do it and what the record holds.
- **Required Human Review**: Privacy review by Ives; Nina confirms the reason
- **Open Decisions**: None
