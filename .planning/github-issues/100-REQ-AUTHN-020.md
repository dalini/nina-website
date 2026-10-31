## Metadata

- **ID**: REQ-AUTHN-020
- **Title**: Verify the OAuth state of every sign-in with __Host- cookies and reject callbacks with more than one state cookie
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-AUTHN-4 (`state` verified, `__Host-` cookies, more than one state cookie rejected), SEC-AUTHN-3 (forgery protection), `REF-COOKIE-PREFIX`, `REF-SVELTIA-AUTH`; T-43, T-78, T-3; parent {{ISSUE_URL:REQ-AUTHN-000}}

## Requirement

- **Statement**: The OAuth helper MUST verify the OAuth `state` value of every sign-in, MUST keep every cookie it sets under the `__Host-` prefix (`Secure`, `Path=/`, no `Domain`), and MUST reject a callback whose request carries more than one state cookie, so that a cookie planted from another host of the site cannot fix or disrupt the sign-in.
- **Rationale**: `nina-laaf.de`, `edit.` and `preview.` are one site for cookies, so script in the public origin can set cookies the browser sends to the helper (T-78); the `state` check protects the sign-in flow against forgery (T-43, SEC-AUTHN-3).
- **Assumptions**: None
- **Out of Scope**: Token release and scopes (REQ-AUTHN-010); tokens in URLs and logs (REQ-AUTHN-040).
- **Design Traceability**: N/A
- **Architecture Traceability**: Editing Interface (OAuth helper); DR-9 (separate origins, one site for cookies)
- **Security Traceability**: SEC-AUTHN-4, SEC-AUTHN-3

## Scope

- **Applies To**: Server-Side Application
- **Components**: Editing Interface (OAuth helper on `edit.nina-laaf.de`), Static Hosting
- **Interfaces / Operations**: Sign-in start (sets the state cookie) and OAuth callback (checks it)
- **Actors**: Editors (Nina, Ives); GitHub OAuth; an attacker's website (abuse case)
- **Preconditions**: REQ-AUTHN-010
- **Data Classification**: Restricted
- **Personal or Regulated Data**: Personal Data (editor GitHub identities and OAuth tokens)
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Authenticity, Integrity
- **Control Layers**: Session Management, Authentication
- **Threat References**: STRIDE Spoofing and Denial of Service: SECURITY.md Threat Model v3 T-43, T-78; CWE-352, CWE-384
- **Abuse / Misuse Case**: The Google tag or injected markup on the public origin plants a state cookie so that sign-ins fail or the check compares against an attacker's value
- **Trust Boundary**: TB-2 (editor browser ↔ Editing Interface), TB-3 (Editing Interface ↔ Content Store via GitHub)
- **Untrusted Inputs or Assertions**: Cookies on the callback request; the `state` query parameter
- **Authoritative Enforcement Point**: OAuth helper on `edit.nina-laaf.de` (Deno port of the Sveltia CMS Authenticator)
- **Independent Verification**: Browser tests driven from other origins against the test helper; GitHub's authorization records
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: Cookie prefixes draft (`REF-COOKIE-PREFIX`); OWASP Session Management Cheat Sheet (`REF-SESSION`)
- **Mapping Basis**: Other: `REF-COOKIE-PREFIX` and `REF-SESSION` as cited in SECURITY.md SEC-AUTHN-4 and SEC-AUTHN-3.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given a sign-in started on `https://edit.nina-laaf.de`, when the callback returns with the matching `state`, then the sign-in completes.
2. **AC-02 — Boundary or failure behavior**: Given a callback with a wrong or missing `state`, when it arrives, then it is rejected and no token is released.
3. **AC-03 — Boundary or failure behavior**: Given script on the public test origin has set a `csrf-token` or state cookie for `.nina-laaf.de` with the callback path, when the editor signs in, then the callback carries more than one state cookie and is rejected, and no attacker-chosen value is accepted as `state`.
4. **AC-04 — Prohibited behavior**: Given every cookie the helper sets, when its attributes are inspected, then each uses the `__Host-` prefix with `Secure`, `Path=/` and no `Domain`.

## Failure Behavior

- **On Invalid Input**: A wrong, missing or duplicated state is rejected with an error page; no token is released
- **On Authentication Failure**: The sign-in fails; the editor can start again
- **On Authorization Failure**: Denied by the operating system or the platform; nothing changes
- **On Security-Decision Failure**: Deny by default: any doubt about the state rejects the callback
- **On External Dependency Failure**: N/A
- **On System Error**: The job stops with a non-zero exit status, changes nothing it has not finished, and leaves the previous state in place
- **Logging / Audit**: Rejected callbacks are logged with the reason, without codes, tokens or cookie values
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: State generation and comparison; cookie attribute builder; duplicate-cookie detection
- **Integration Tests**: Sign-in flows on the test hosts with and without planted cookies
- **Security Tests**: Planted parent-domain cookies with longer paths; replayed and missing `state`
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:AUTHN-020/AC-01`; AC-02 → `test:AUTHN-020/AC-02`; AC-03 → `test:AUTHN-020/AC-03`; AC-04 → `test:AUTHN-020/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: The helper under Deno on a test `edit.` host and a public test origin of the same registrable domain, with a test GitHub OAuth App; Chrome, Edge, Firefox and Safari in their last two major versions

## Dependencies

- **Upstream Requirements**: REQ-AUTHN-010 ({{ISSUE_URL:REQ-AUTHN-010}})
- **Downstream Requirements**: REQ-AUTHN-040 ({{ISSUE_URL:REQ-AUTHN-040}})
- **External Dependencies**: GitHub OAuth
- **Dependency Assumptions**: Browsers enforce the `__Host-` prefix rules (`REF-COOKIE-PREFIX`)
- **Failure Impact**: A broken check either blocks sign-ins (visible) or accepts forged ones (silent)

## Implementation Notes

- **Constraints**: Deno port of the Sveltia CMS Authenticator (`REF-SVELTIA-AUTH`) in `dalini/nina-site`, maintained by Ives, with a pinned Deno version and dependencies from a lockfile (SEC-CICD-2, DEP-1 to DEP-8); the client secret is read from the helper's own secret file (SEC-SECRET-1)
- **Prohibited Approaches**: Releasing a token to a page whose origin was not compared in full; reading the OAuth `state` from a cookie without the `__Host-` prefix; logging codes, tokens or the client secret; `--allow-all`
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Cookie-scoping subtleties across hosts of one site (T-78) and CSRF on the sign-in flow need careful, adversarial tests.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
