## Metadata

- **ID**: REQ-AUTHN-050
- **Title**: Revoke all Editing Interface tokens of an editor at once, and drill it
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-AUTHN-5, SEC-AUTHN-3 (accepted exception limited by revocation), SEC-AUTHZ-2; T-44, T-47; parent {{ISSUE_URL:REQ-AUTHN-000}}

## Requirement

- **Statement**: Ives MUST be able to revoke all Editing Interface tokens of an editor at once (by revoking that editor's authorization of the OAuth App, or all tokens of the app), and MUST do so when an editor reports a lost device, when an editor is removed, and after a suspected compromise; the procedure MUST be documented and drilled.
- **Rationale**: Tokens do not expire (accepted exception, SEC-AUTHN-3); revocation is what limits a stolen token (T-44, T-47).
- **Assumptions**: None
- **Out of Scope**: Rotating the OAuth App's client secret (SEC-SECRET-1 yearly rotation).
- **Design Traceability**: N/A
- **Architecture Traceability**: Editing Interface (GitHub OAuth App); Content Store (TB-3)
- **Security Traceability**: SEC-AUTHN-5

## Scope

- **Applies To**: Server-Side Application
- **Components**: Editing Interface, Content Store
- **Interfaces / Operations**: GitHub OAuth App settings (revoke user tokens, revoke all); revocation runbook in `docs/ops/`
- **Actors**: Maintainer (Ives); editors
- **Preconditions**: REQ-AUTHN-010
- **Data Classification**: Restricted
- **Personal or Regulated Data**: Personal Data (editor GitHub identities and OAuth tokens)
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Confidentiality, Integrity
- **Control Layers**: Session Management, Authorization
- **Threat References**: STRIDE Elevation of Privilege and Spoofing: SECURITY.md Threat Model v3 T-44, T-47; CWE-613
- **Abuse / Misuse Case**: A token copied from a lost device keeps working indefinitely
- **Trust Boundary**: TB-3 (Editing Interface ↔ Content Store)
- **Untrusted Inputs or Assertions**: Tokens on lost or compromised devices
- **Authoritative Enforcement Point**: GitHub (token revocation)
- **Independent Verification**: API calls with the revoked token after the drill
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP Session Management Cheat Sheet (`REF-SESSION`); OWASP Authentication Cheat Sheet (`REF-AUTH`)
- **Mapping Basis**: Other: `REF-SESSION` and `REF-AUTH` as cited in SECURITY.md SEC-AUTHN-5.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given a test editor signed in on two devices, when Ives revokes that editor's authorization of the OAuth App, then the Editing Interface on both devices can no longer read or write the test content repository.
2. **AC-02 — Boundary or failure behavior**: Given a suspected compromise, when Ives revokes all tokens of the OAuth App, then every editor's Editing Interface stops working until they sign in again.
3. **AC-03 — Prohibited behavior**: Given the runbook in `docs/ops/`, when it is reviewed, then it requires revocation on a lost device report, on editor removal (SEC-AUTHZ-2) and after a suspected compromise, and records each drill without tokens.

## Failure Behavior

- **On Invalid Input**: N/A
- **On Authentication Failure**: After revocation the Editing Interface asks for a new sign-in
- **On Authorization Failure**: Denied by the operating system or the platform; nothing changes
- **On Security-Decision Failure**: Deny by default
- **On External Dependency Failure**: GitHub unavailable: revocation waits; editing is also unavailable then
- **On System Error**: N/A
- **Logging / Audit**: Drill and revocation records in `docs/ops/` (date, reason kind, no tokens); GitHub's authorization records
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: N/A: runbook
- **Integration Tests**: Revocation drill with a test editor and a test OAuth App
- **Security Tests**: Use of the revoked token against the GitHub API
- **Compliance Tests / Evidence**: Drill record in `docs/ops/`
- **Acceptance-Criteria Traceability**: AC-01 → `test:AUTHN-050/AC-01`; AC-02 → `test:AUTHN-050/AC-02`; AC-03 → `test:AUTHN-050/AC-03` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: The helper running under Deno on a test host with a test GitHub OAuth App, a test editor account and a test content repository (not `dalini/nina-content`); a second test origin serving attacker pages; Chrome, Edge, Firefox and Safari in their last two major versions

## Dependencies

- **Upstream Requirements**: REQ-AUTHN-010 ({{ISSUE_URL:REQ-AUTHN-010}})
- **Downstream Requirements**: None
- **External Dependencies**: GitHub OAuth App token revocation
- **Dependency Assumptions**: Revoking an authorization invalidates every token of that user for the app
- **Failure Impact**: If revocation is missed, a stolen token keeps access

## Implementation Notes

- **Constraints**: Deno port of the Sveltia CMS Authenticator (`REF-SVELTIA-AUTH`) in `dalini/nina-site`, maintained by Ives, with a pinned Deno version and dependencies from a lockfile (SEC-CICD-2, DEP-1 to DEP-8); the client secret is read from the helper's own secret file (SEC-SECRET-1)
- **Prohibited Approaches**: Releasing a token to a page whose origin was not compared in full; reading the OAuth `state` from a cookie without the `__Host-` prefix; logging codes, tokens or the client secret; `--allow-all`
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: A runbook and a drill using GitHub's own revocation; fully specified by SEC-AUTHN-5.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
