## Metadata

- **ID**: REQ-PIPELINE-140
- **Title**: Restrict the production and preview deploy keys to the deploy's file transfers and reads, with no shell, other command or forwarding
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-CICD-1 (deploy keys: only the deploy's transfers and reads; no shell, command or forwarding; no write to the refresh job or scheduled jobs), SEC-SECRET-2; T-81; parent {{ISSUE_URL:REQ-PIPELINE-000}}

## Requirement

- **Statement**: The production and preview deploy keys MUST allow only the file transfers of the deploy and the reads listed in SEC-CICD-1 (production: write the web root and the publish status file, read the Instagram token and the deployed feed files; preview: write the preview web root): no interactive shell, no other command and no port forwarding; and the paths the production deploy key can write MUST NOT include the Instagram refresh job or the deploy account's scheduled jobs.
- **Rationale**: A leaked key must not be able to change the job that uses the Instagram token, add scheduled jobs that survive rotation, or use the VPS as a relay (T-81).
- **Assumptions**: None
- **Out of Scope**: The deploy account's paths and non-root status (REQ-PIPELINE-010); where the keys are stored (REQ-PIPELINE-050).
- **Design Traceability**: N/A
- **Architecture Traceability**: Site Build (deploy over rsync/SSH with restricted keys); Static Hosting
- **Security Traceability**: SEC-CICD-1, SEC-SECRET-2

## Scope

- **Applies To**: Server-Side Application
- **Components**: Static Hosting (VPS), Site Build pipeline
- **Interfaces / Operations**: SSH `authorized_keys` restrictions of the deploy account(s); rsync
- **Actors**: Build identity (production and preview deploy keys); an attacker with a leaked key (abuse case)
- **Preconditions**: REQ-PIPELINE-010, REQ-PIPELINE-060, REQ-HOST-030
- **Data Classification**: Restricted
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity, Confidentiality
- **Control Layers**: Authorization
- **Threat References**: STRIDE Elevation of Privilege: SECURITY.md Threat Model v3 T-81, T-57, T-62; CWE-250
- **Abuse / Misuse Case**: A leaked deploy key opens a shell, edits the refresh job to send the token elsewhere, or forwards ports
- **Trust Boundary**: TB-4 (Site Build → Static Hosting over SSH)
- **Untrusted Inputs or Assertions**: Commands and options sent by an SSH client holding a deploy key
- **Authoritative Enforcement Point**: sshd on the VPS (key restrictions and forced command)
- **Independent Verification**: SSH sessions with each key that try every refused action
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP CI/CD Security Cheat Sheet (`REF-CICD`)
- **Mapping Basis**: Other: `REF-CICD` as cited in SECURITY.md SEC-CICD-1.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given the production deploy key, when the deploy runs, then it can write the `nina-laaf.de` web root and the publish status file and read the Instagram token and the deployed feed files; given the preview deploy key, it can write only the preview web root.
2. **AC-02 — Boundary or failure behavior**: Given each deploy key, when an SSH session asks for an interactive shell, runs another command, or requests local or remote port forwarding, then the server refuses it.
3. **AC-03 — Boundary or failure behavior**: Given the production deploy key, when it tries to write the Instagram refresh job, the deploy account's crontab or any other scheduled-job path, then the write is refused.
4. **AC-04 — Prohibited behavior**: Given the key restrictions, when they are reviewed, then no key allows a path outside those listed in SEC-CICD-1, and no restriction depends on the client behaving well.

## Failure Behavior

- **On Invalid Input**: N/A
- **On Authentication Failure**: A key that is not listed is refused by sshd
- **On Authorization Failure**: sshd or the forced command refuses the action; nothing is written
- **On Security-Decision Failure**: Deny by default
- **On External Dependency Failure**: N/A
- **On System Error**: The job stops with a non-zero exit status, changes nothing it has not finished, and leaves the previous state in place
- **Logging / Audit**: sshd logs refused sessions and commands for the deploy account
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: N/A: configuration (a forced-command wrapper, if one is written, gets tests for each allowed and refused request)
- **Integration Tests**: Deploy runs of both builds with the restricted keys against the test VPS
- **Security Tests**: Shell, command, forwarding and write attempts with each key
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:PIPELINE-140/AC-01`; AC-02 → `test:PIPELINE-140/AC-02`; AC-03 → `test:PIPELINE-140/AC-03`; AC-04 → `test:PIPELINE-140/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test VPS (or the VPS before go-live) with the accounts and file layout of REQ-HOST-030, and a test content repository (not `dalini/nina-content`)

## Dependencies

- **Upstream Requirements**: REQ-PIPELINE-010 ({{ISSUE_URL:REQ-PIPELINE-010}}), REQ-PIPELINE-060 ({{ISSUE_URL:REQ-PIPELINE-060}}), REQ-HOST-030 ({{ISSUE_URL:REQ-HOST-030}})
- **Downstream Requirements**: None
- **External Dependencies**: OpenSSH on the VPS
- **Dependency Assumptions**: sshd enforces `authorized_keys` restrictions and forced commands
- **Failure Impact**: A wrongly restricted key breaks the deploy (visible) or allows too much (silent)

## Implementation Notes

- **Constraints**: Configuration and scripts kept in `dalini/nina-site`; runbooks and review records in `docs/ops/` of `dalini/nina-website`, with no secrets and no personal data beyond the editors' names (SEC-SECRET-1); DEP-1 to DEP-8 apply to every package added
- **Prohibited Approaches**: Settings applied by hand without being kept in the reviewed configuration or runbook; secrets in scripts, command lines, logs or `docs/ops/`
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: SSH key restrictions (forced commands, path limits) are easy to get subtly wrong, and a gap turns a leaked key into a persistent foothold (T-81).
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
