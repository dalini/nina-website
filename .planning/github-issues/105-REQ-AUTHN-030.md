## Metadata

- **ID**: REQ-AUTHN-030
- **Title**: Run the OAuth helper under its own account with a pinned Deno version, pinned dependencies and only the Deno permissions it needs
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 fourth run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-AUTHN-4 (own unprivileged account, pinned Deno and dependencies, minimal permissions), SEC-HOST-2, SEC-CICD-2 (helper code pinned), DEP-1 to DEP-8, `REF-DENO-PERM`; ARCHITECTURE.md Editing Interface; T-80, T-61, T-46; parent {{ISSUE_URL:REQ-AUTHN-000}}

## Requirement

- **Statement**: The OAuth helper MUST run on the VPS under its own unprivileged account with a pinned Deno version and dependencies pinned in a lockfile, and Deno MUST grant it only network access to its listening address and GitHub's OAuth endpoint, read access to its own code and its own secret file, and its own environment variables; never `--allow-all`, and no permission to write files, run programs or load native libraries.
- **Rationale**: A flaw in the helper or its dependencies acts with the permissions it is started with; with `--allow-all` Deno's sandbox is off (T-80).
- **Assumptions**: None
- **Out of Scope**: Installing the helper's code and lockfile on the VPS from a tagged release (REQ-EDIT-100); the account and secret file layout (REQ-HOST-030).
- **Design Traceability**: N/A
- **Architecture Traceability**: Editing Interface (helper run on the VPS under its own account with a pinned Deno version); Static Hosting
- **Security Traceability**: SEC-AUTHN-4, SEC-HOST-2, SEC-CICD-2

## Scope

- **Applies To**: Server-Side Application
- **Components**: Editing Interface (OAuth helper on `edit.nina-laaf.de`), Static Hosting
- **Interfaces / Operations**: Service definition and start command of the helper; Deno permission flags; lockfile
- **Actors**: OAuth helper account; maintainer (Ives)
- **Preconditions**: REQ-AUTHN-010, REQ-HOST-030
- **Data Classification**: Restricted
- **Personal or Regulated Data**: Personal Data (editor GitHub identities and OAuth tokens)
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity, Confidentiality
- **Control Layers**: Architecture, Supply Chain
- **Threat References**: STRIDE Elevation of Privilege and Tampering: SECURITY.md Threat Model v3 T-80, T-61, T-46; CWE-250, CWE-1104
- **Abuse / Misuse Case**: A dependency or a parsing flaw in the helper tries to read other secrets, run programs or connect elsewhere
- **Trust Boundary**: TB-2 (editor browser ↔ Editing Interface), TB-3 (Editing Interface ↔ Content Store via GitHub)
- **Untrusted Inputs or Assertions**: HTTP requests to the helper; third-party dependency code
- **Authoritative Enforcement Point**: Deno's permission system and the operating system account
- **Independent Verification**: Permission-probe test build run under the production flags
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: Deno security and permissions (`REF-DENO-PERM`); OWASP Software Supply Chain Security Cheat Sheet (`REF-SUPPLY`)
- **Mapping Basis**: Other: `REF-DENO-PERM` and `REF-SUPPLY` as cited in SECURITY.md SEC-AUTHN-4 and SEC-CICD-2.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given the helper's start command, when it is reviewed, then Deno runs with network access only to its listening address and GitHub's OAuth endpoint, read access only to its own code and its own secret file, and only its own environment variables, under its own unprivileged account, with a pinned Deno version and dependencies from the lockfile.
2. **AC-02 — Boundary or failure behavior**: Given a test build of the helper that tries to write a file, run a program, load a native library, read another account's file or connect to another host, when it runs with the configured permissions, then Deno denies each attempt.
3. **AC-03 — Prohibited behavior**: Given the start command and the service definition, when they are reviewed, then `--allow-all` and unrestricted `--allow-net`, `--allow-read`, `--allow-write`, `--allow-run` or `--allow-ffi` are absent, and the helper does not run as root.

## Failure Behavior

- **On Invalid Input**: N/A: request handling is REQ-AUTHN-010 and REQ-AUTHN-020
- **On Authentication Failure**: N/A
- **On Authorization Failure**: Denied by the operating system or the platform; nothing changes
- **On Security-Decision Failure**: Deny by default: a permission the helper lacks makes the operation fail
- **On External Dependency Failure**: N/A
- **On System Error**: The service restarts the helper; a start with missing permissions fails visibly instead of widening them
- **Logging / Audit**: Service start and permission errors are logged without secrets
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: N/A: configuration
- **Integration Tests**: Helper started with the production flags on the test VPS completes a sign-in (REQ-AUTHN-010 tests)
- **Security Tests**: Permission-probe build; review of the start command and service definition
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:AUTHN-030/AC-01`; AC-02 → `test:AUTHN-030/AC-02`; AC-03 → `test:AUTHN-030/AC-03` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: The helper running under Deno on a test host with a test GitHub OAuth App, a test editor account and a test content repository (not `dalini/nina-content`); a second test origin serving attacker pages; Chrome, Edge, Firefox and Safari in their last two major versions

## Dependencies

- **Upstream Requirements**: REQ-AUTHN-010 ({{ISSUE_URL:REQ-AUTHN-010}}), REQ-HOST-030 ({{ISSUE_URL:REQ-HOST-030}})
- **Downstream Requirements**: REQ-EDIT-100 ({{ISSUE_URL:REQ-EDIT-100}})
- **External Dependencies**: Deno (pinned version); the helper's dependencies (lockfile)
- **Dependency Assumptions**: Deno enforces its permission flags as documented (`REF-DENO-PERM`)
- **Failure Impact**: Too wide permissions let one flaw reach the VPS; too narrow ones stop sign-ins (visible)

## Implementation Notes

- **Constraints**: Deno port of the Sveltia CMS Authenticator (`REF-SVELTIA-AUTH`) in `dalini/nina-site`, maintained by Ives, with a pinned Deno version and dependencies from a lockfile (SEC-CICD-2, DEP-1 to DEP-8); the client secret is read from the helper's own secret file (SEC-SECRET-1)
- **Prohibited Approaches**: Releasing a token to a page whose origin was not compared in full; reading the OAuth `state` from a cookie without the `__Host-` prefix; logging codes, tokens or the client secret; `--allow-all`
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Least-privilege runtime configuration for the only internet-facing server-side code (T-80, T-61).
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None. Decided in step 10 a third time (`.planning/OPEN_QUESTIONS.md`): PQ-54: Ives installs the Editing Interface files by hand from a tagged, verified release of `dalini/nina-site`; root-owned, read-only to nginx and the helper; no deploy key writes them (SEC-CICD-2). The installation is REQ-EDIT-100.
