## Metadata

- **ID**: REQ-AUTHN-010
- **Title**: Sign editors in through the Deno OAuth helper, request only the allowed scopes, and release the token only to https://edit.nina-laaf.de
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 fourth run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-AUTHN-1 (Editing Interface), SEC-AUTHN-4 (allowed domains, full origin, scopes, no `workflow`, client secret), SEC-AUTHZ-3, SEC-SECRET-1, `REF-SVELTIA-AUTH`; REQUIREMENTS.md FR-10.5; ARCHITECTURE.md Editing Interface, DR-9; T-43, T-80, T-11, T-44; parent {{ISSUE_URL:REQ-AUTHN-000}}

## Requirement

- **Statement**: Editors MUST sign in to the Editing Interface through the OAuth helper on `edit.nina-laaf.de`, a Deno port of the Sveltia CMS Authenticator kept in `dalini/nina-site`; the helper MUST request only the scopes SEC-AUTHZ-3 allows and never `workflow`, MUST read its GitHub OAuth client secret from its own secret file, and MUST release a token only to pages served from `https://edit.nina-laaf.de`, comparing the full origin (scheme and host); an empty allowed-domains setting MUST release no token.
- **Rationale**: Nina cannot edit without a sign-in; the upstream authenticator releases tokens to any page when the setting is empty and compares host names only (T-80), and another site must not receive an editor's token (T-43).
- **Assumptions**: The GitHub OAuth App keeps its default scope `repo,user` (accepted risk, SEC-AUTHZ-3)
- **Out of Scope**: The `state` check and its cookies (REQ-AUTHN-020); the helper's process, account and Deno permissions (REQ-AUTHN-030); installing the helper's code on the VPS (REQ-EDIT-100).
- **Design Traceability**: N/A
- **Architecture Traceability**: Editing Interface (GitHub OAuth helper on `edit.nina-laaf.de`, Deno port in `dalini/nina-site`); DR-9; DF-10
- **Security Traceability**: SEC-AUTHN-1, SEC-AUTHN-4, SEC-AUTHZ-3, SEC-SECRET-1

## Scope

- **Applies To**: Server-Side Application
- **Components**: Editing Interface (OAuth helper on `edit.nina-laaf.de`), Static Hosting
- **Interfaces / Operations**: Helper endpoints for sign-in start and OAuth callback; the message that hands the token to the Editing Interface page
- **Actors**: Editors (Nina, Ives); GitHub OAuth; an attacker's website (abuse case)
- **Preconditions**: REQ-EDIT-010, REQ-HTTP-040
- **Data Classification**: Restricted
- **Personal or Regulated Data**: Personal Data (editor GitHub identities and OAuth tokens)
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Authenticity, Confidentiality
- **Control Layers**: Authentication, Authorization
- **Threat References**: STRIDE Spoofing and Elevation of Privilege: SECURITY.md Threat Model v3 T-43, T-80, T-11, T-44; CWE-346, CWE-601
- **Abuse / Misuse Case**: Another website opens the helper's sign-in window and receives the editor's token from the message the helper sends back
- **Trust Boundary**: TB-2 (editor browser ↔ Editing Interface), TB-3 (Editing Interface ↔ Content Store via GitHub)
- **Untrusted Inputs or Assertions**: The origin of the page that opened the sign-in; callback parameters from the browser
- **Authoritative Enforcement Point**: OAuth helper on `edit.nina-laaf.de` (Deno port of the Sveltia CMS Authenticator)
- **Independent Verification**: Browser tests driven from other origins against the test helper; GitHub's authorization records
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP Authentication Cheat Sheet (`REF-AUTH`); Sveltia CMS Authenticator (`REF-SVELTIA-AUTH`)
- **Mapping Basis**: Other: `REF-AUTH` and `REF-SVELTIA-AUTH` as cited in SECURITY.md SEC-AUTHN-4.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given a signed-out test editor on `https://edit.nina-laaf.de`, when she signs in with GitHub, then the helper exchanges the code using its client secret read from its own secret file, and the token reaches the Editing Interface page on that origin only, which can then read and write the test content repository.
2. **AC-02 — Boundary or failure behavior**: Given a sign-in started from a page on another origin, or from `http://edit.nina-laaf.de`, when the callback completes, then no token is posted to that page.
3. **AC-03 — Boundary or failure behavior**: Given the allowed-domains setting is empty, when any sign-in completes, then no token is released.
4. **AC-04 — Prohibited behavior**: Given GitHub's authorization screen, when it is reviewed, then the helper requests the default `repo,user` scope of the OAuth App (accepted, SEC-AUTHZ-3) and never `workflow`; and the token issued to Nina's test account is rejected when it tries to push to a test copy of `dalini/nina-site` (SEC-AUTHZ-3, DR-8).
5. **AC-05 — Additional criterion**: Given no sign-in, when the Editing Interface is opened or the Content Store is requested through the GitHub API without a token, then no unpublished content is listed or returned (SEC-AUTHN-1).

## Failure Behavior

- **On Invalid Input**: A callback without a valid code is rejected; no token is released
- **On Authentication Failure**: A failed GitHub sign-in shows an error page; no token is released; nothing reveals which accounts exist
- **On Authorization Failure**: Denied by the operating system or the platform; nothing changes
- **On Security-Decision Failure**: Deny by default: an origin that does not match exactly, or an empty setting, releases no token
- **On External Dependency Failure**: GitHub unreachable: the sign-in fails with an error; the public site stays online (T-10)
- **On System Error**: The job stops with a non-zero exit status, changes nothing it has not finished, and leaves the previous state in place
- **Logging / Audit**: The helper logs sign-in results without codes, tokens or the client secret; GitHub records each authorization in the editor's account
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: Origin comparison (scheme, host, port; empty setting); scope list; secret file loading
- **Integration Tests**: Full sign-in against a test OAuth App with the test Editing Interface
- **Security Tests**: Attacker-origin and `http:` sign-in attempts; empty setting; push to a test copy of `dalini/nina-site` with Nina's test token
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:AUTHN-010/AC-01`; AC-02 → `test:AUTHN-010/AC-02`; AC-03 → `test:AUTHN-010/AC-03`; AC-04 → `test:AUTHN-010/AC-04`; AC-05 → `test:AUTHN-010/AC-05` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: The helper running under Deno on a test host with a test GitHub OAuth App, a test editor account and a test content repository (not `dalini/nina-content`); a second test origin serving attacker pages; Chrome, Edge, Firefox and Safari in their last two major versions

## Dependencies

- **Upstream Requirements**: REQ-EDIT-010 ({{ISSUE_URL:REQ-EDIT-010}}), REQ-HTTP-040 ({{ISSUE_URL:REQ-HTTP-040}})
- **Downstream Requirements**: REQ-AUTHN-020 ({{ISSUE_URL:REQ-AUTHN-020}}), REQ-AUTHN-030 ({{ISSUE_URL:REQ-AUTHN-030}}), REQ-AUTHN-050 ({{ISSUE_URL:REQ-AUTHN-050}})
- **External Dependencies**: GitHub OAuth (OAuth App)
- **Dependency Assumptions**: GitHub validates the redirect URI registered for the OAuth App
- **Failure Impact**: Editors cannot sign in while GitHub or the helper is down; the public site stays online

## Implementation Notes

- **Constraints**: Deno port of the Sveltia CMS Authenticator (`REF-SVELTIA-AUTH`) in `dalini/nina-site`, maintained by Ives, with a pinned Deno version and dependencies from a lockfile (SEC-CICD-2, DEP-1 to DEP-8); the client secret is read from the helper's own secret file (SEC-SECRET-1)
- **Prohibited Approaches**: Releasing a token to a page whose origin was not compared in full; reading the OAuth `state` from a cookie without the `__Host-` prefix; logging codes, tokens or the client secret; `--allow-all`
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Security-critical port of an OAuth relay where upstream behavior (empty allowed-domains, host-only comparison) must be changed, not copied (T-80, T-43).
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None. Decided in step 10 a third time (`.planning/OPEN_QUESTIONS.md`): PQ-54: Ives installs the Editing Interface files by hand from a tagged, verified release of `dalini/nina-site`; root-owned, read-only to nginx and the helper; no deploy key writes them (SEC-CICD-2). The installation is REQ-EDIT-100.
