## Metadata

- **ID**: REQ-AUTHN-040
- **Title**: Obtain a new token on each sign-in and keep tokens and session values out of URLs and logs
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-AUTHN-3 (new token per sign-in, no tokens in URLs or logs, forgery protection, accepted exception); T-2, T-3, T-47, T-48; parent https://github.com/dalini/nina-website/issues/15

## Requirement

- **Statement**: Each sign-in to the Editing Interface MUST obtain a new token; tokens and session values MUST NOT appear in URLs or logs; state-changing editing actions MUST be protected against cross-site request forgery; and the accepted exception of SEC-AUTHN-3 (tokens that do not expire, no inactivity timeout, no session maximum, cached content kept after sign-out, editors on their own devices only) MUST be documented for the editors.
- **Rationale**: Tokens in URLs or logs leak (T-2); the accepted exception relies on editors using their own devices and on revocation (SEC-AUTHN-5).
- **Assumptions**: None
- **Out of Scope**: Revocation (REQ-AUTHN-050); the `state` check (REQ-AUTHN-020).
- **Design Traceability**: N/A
- **Architecture Traceability**: Editing Interface (OAuth helper, Sveltia CMS); Static Hosting (nginx logs of `edit.`)
- **Security Traceability**: SEC-AUTHN-3

## Scope

- **Applies To**: Server-Side Application
- **Components**: Editing Interface (OAuth helper on `edit.nina-laaf.de`), Static Hosting
- **Interfaces / Operations**: Sign-in flow; helper and nginx logs; editors' runbook
- **Actors**: Editors (Nina, Ives); GitHub OAuth; an attacker's website (abuse case)
- **Preconditions**: REQ-AUTHN-020, REQ-PRIV-020
- **Data Classification**: Restricted
- **Personal or Regulated Data**: Personal Data (editor GitHub identities and OAuth tokens)
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Confidentiality
- **Control Layers**: Session Management, Logging and Monitoring
- **Threat References**: STRIDE Spoofing and Tampering: SECURITY.md Threat Model v3 T-2, T-3, T-47 (accepted), T-48 (accepted); CWE-598, CWE-532, CWE-352
- **Abuse / Misuse Case**: Someone with log access reads a token or OAuth code from the edit host's access log
- **Trust Boundary**: TB-2 (editor browser ↔ Editing Interface), TB-3 (Editing Interface ↔ Content Store via GitHub)
- **Untrusted Inputs or Assertions**: Cross-site requests in the editor's browser
- **Authoritative Enforcement Point**: OAuth helper and nginx log configuration on `edit.nina-laaf.de`
- **Independent Verification**: Search of logs and history after a recorded sign-in
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP Session Management Cheat Sheet (`REF-SESSION`)
- **Mapping Basis**: Other: `REF-SESSION` as cited in SECURITY.md SEC-AUTHN-3.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given an editor signs in twice, when the two tokens are compared, then each sign-in obtained a new token.
2. **AC-02 — Boundary or failure behavior**: Given a full sign-in and an editing session, when the browser history, the helper's logs and the nginx access logs of `edit.nina-laaf.de` are searched for the token and the OAuth code, then neither appears.
3. **AC-03 — Boundary or failure behavior**: Given a cross-site page that submits a form or request to the helper or replays a callback, when it runs in the editor's browser, then no state-changing editing action happens (REQ-AUTHN-020 rejects the callback; editing calls need the token in a request header).
4. **AC-04 — Prohibited behavior**: Given the editors' runbook in `docs/ops/`, when it is reviewed, then it states the accepted exception of SEC-AUTHN-3 (no inactivity timeout, no session maximum, cached content kept after sign-out) and that editors sign in only on their own devices.

## Failure Behavior

- **On Invalid Input**: N/A
- **On Authentication Failure**: N/A: handled by REQ-AUTHN-010
- **On Authorization Failure**: Denied by the operating system or the platform; nothing changes
- **On Security-Decision Failure**: Deny by default
- **On External Dependency Failure**: N/A
- **On System Error**: The job stops with a non-zero exit status, changes nothing it has not finished, and leaves the previous state in place
- **Logging / Audit**: nginx and helper logs of `edit.` hold no query strings of the callback and no tokens
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: Log redaction of callback query strings
- **Integration Tests**: Two sign-ins with the test OAuth App; log and history search
- **Security Tests**: Cross-site request and callback replay from the attacker test origin
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:AUTHN-040/AC-01`; AC-02 → `test:AUTHN-040/AC-02`; AC-03 → `test:AUTHN-040/AC-03`; AC-04 → `test:AUTHN-040/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: The helper running under Deno on a test host with a test GitHub OAuth App, a test editor account and a test content repository (not `dalini/nina-content`); a second test origin serving attacker pages; Chrome, Edge, Firefox and Safari in their last two major versions

## Dependencies

- **Upstream Requirements**: REQ-AUTHN-020 (https://github.com/dalini/nina-website/issues/106), REQ-PRIV-020 (https://github.com/dalini/nina-website/issues/67)
- **Downstream Requirements**: None
- **External Dependencies**: GitHub OAuth
- **Dependency Assumptions**: GitHub issues a new OAuth App token on each code exchange
- **Failure Impact**: A token in a log can be used until it is revoked (SEC-AUTHN-5)

## Implementation Notes

- **Constraints**: Deno port of the Sveltia CMS Authenticator (`REF-SVELTIA-AUTH`) in `dalini/nina-site`, maintained by Ives, with a pinned Deno version and dependencies from a lockfile (SEC-CICD-2, DEP-1 to DEP-8); the client secret is read from the helper's own secret file (SEC-SECRET-1)
- **Prohibited Approaches**: Releasing a token to a page whose origin was not compared in full; reading the OAuth `state` from a cookie without the `__Host-` prefix; logging codes, tokens or the client secret; `--allow-all`
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Verification-heavy work over known places (URLs, nginx and helper logs) with a documented, accepted exception.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
