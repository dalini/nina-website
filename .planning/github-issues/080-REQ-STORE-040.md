## Metadata

- **ID**: REQ-STORE-040
- **Title**: Protect the history of main in dalini/nina-content and require verified commits there
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 fourth run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-AUDIT-1 (attribution from the sign-in, history not rewritable, verified commits checked at implementation, paid plan), `REF-GH-PLANS`; REQUIREMENTS.md FR-10.7; T-9, T-15, T-52, T-53; parent {{ISSUE_URL:REQ-STORE-000}}

## Requirement

- **Statement**: `main` of `dalini/nina-content` MUST be protected so that editors, the Editing Interface and the build identity cannot rewrite or delete its history, and every change on it MUST record the authenticated editor from the Editing Interface sign-in and the time; `main` MUST accept only commits that GitHub marks as verified, unless the check at implementation shows that commits the Editing Interface makes through GitHub's API are not marked verified, in which case this setting is not used and the result is recorded.
- **Rationale**: Nina must be able to tell who changed what and undo it (FR-10.7); history that can be rewritten hides tampering (T-15); self-declared authors on direct pushes are T-53.
- **Assumptions**: `dalini` is a personal account on GitHub Pro, so branch protection on a private repository is available (step 10 again; account type checked in step 10 a third time)
- **Out of Scope**: The history view and restore (REQ-EDIT-080); the purge procedure (REQ-STORE-060).
- **Design Traceability**: N/A
- **Architecture Traceability**: Content Store (single source of truth with change history)
- **Security Traceability**: SEC-AUDIT-1

## Scope

- **Applies To**: Server-Side Application
- **Components**: Content Store, Editing Interface
- **Interfaces / Operations**: Branch protection or ruleset of `main`; commit verification status
- **Actors**: Editors (Nina, Ives); Editing Interface credential; build identity; maintainer (Ives, bypass)
- **Preconditions**: REQ-STORE-010
- **Data Classification**: Internal
- **Personal or Regulated Data**: Personal Data (editor identities in the history)
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Accountability, Integrity
- **Control Layers**: Authorization, Logging and Monitoring
- **Threat References**: STRIDE Repudiation: SECURITY.md Threat Model v3 T-9, T-15, T-52, T-53; CWE-345
- **Abuse / Misuse Case**: A stolen editor token force-pushes `main` to hide a change, or pushes a commit naming another editor
- **Trust Boundary**: TB-3 (Editing Interface ↔ Content Store)
- **Untrusted Inputs or Assertions**: Self-declared Git author names on direct pushes
- **Authoritative Enforcement Point**: GitHub branch protection or ruleset on `dalini/nina-content`
- **Independent Verification**: Push attempts with each credential against the test repository
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: GitHub Docs on protected branches for private repositories (`REF-GH-PLANS`)
- **Mapping Basis**: Other: `REF-GH-PLANS` as cited in SECURITY.md SEC-AUDIT-1.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given branch protection on `main` of the test content repository, when editors save through the Editing Interface, then their commits land and each shows the signed-in editor and the time.
2. **AC-02 — Boundary or failure behavior**: Given an editor token, the Editing Interface's credential and the build identity, when each tries to force-push `main` or delete it, then GitHub rejects it and the history is unchanged.
3. **AC-03 — Boundary or failure behavior**: Given commits made by the Editing Interface through GitHub's API, when their verification status is checked, then the result is recorded: if they are marked verified, required verified commits are switched on and a direct push of an unsigned commit naming another editor is rejected; if not, the setting stays off and the record names the accepted risk (T-53).
4. **AC-04 — Prohibited behavior**: Given the protection settings, when they are reviewed, then only Ives can bypass them, and that bypass is used only for a documented purge (REQ-STORE-060).

## Failure Behavior

- **On Invalid Input**: N/A
- **On Authentication Failure**: N/A: GitHub authenticates every push
- **On Authorization Failure**: GitHub rejects the push; the history is unchanged
- **On Security-Decision Failure**: Deny by default
- **On External Dependency Failure**: N/A
- **On System Error**: The job stops with a non-zero exit status, changes nothing it has not finished, and leaves the previous state in place
- **Logging / Audit**: The Git history records the editor and time of every change; the verified-commits check result is recorded in `docs/ops/`
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: N/A: repository settings
- **Integration Tests**: Saves by two test editors; force-push and delete attempts with each credential
- **Security Tests**: Direct push of an unsigned commit with a forged author (when verified commits are required)
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:STORE-040/AC-01`; AC-02 → `test:STORE-040/AC-02`; AC-03 → `test:STORE-040/AC-03`; AC-04 → `test:STORE-040/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test content repository under the same plan and protection settings as `dalini/nina-content`, a test Sveltia CMS instance (REQ-EDIT-010) and two test editor accounts

## Dependencies

- **Upstream Requirements**: REQ-STORE-010 ({{ISSUE_URL:REQ-STORE-010}})
- **Downstream Requirements**: REQ-STORE-060 ({{ISSUE_URL:REQ-STORE-060}})
- **External Dependencies**: GitHub (branch protection, commit verification)
- **Dependency Assumptions**: GitHub enforces the protection for every credential except the configured bypass
- **Failure Impact**: Without protection, history can be rewritten and attribution lost

## Implementation Notes

- **Constraints**: Configuration and scripts kept in `dalini/nina-site`; runbooks and review records in `docs/ops/` of `dalini/nina-website`, with no secrets and no personal data beyond the editors' names (SEC-SECRET-1); DEP-1 to DEP-8 apply to every package added
- **Prohibited Approaches**: Settings applied by hand without being kept in the reviewed configuration or runbook; secrets in scripts, command lines, logs or `docs/ops/`
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Branch protection settings with negative tests, plus one documented check whose result decides whether a setting stays.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
