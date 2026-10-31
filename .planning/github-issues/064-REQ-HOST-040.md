## Metadata

- **ID**: REQ-HOST-040
- **Title**: Run a local mail relay agent under its own unprivileged account that alone reads the SMTP relay credential, and deliver the VPS alerts to Ives
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 fourth run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-HTTP-5 (email alerts through a local mail relay agent, step 10 a third time, PQ-56), SEC-SECRET-1 (the SMTP relay credential belongs to the mail relay agent's account), SEC-HOST-2 (the mail relay agent runs unprivileged); ARCHITECTURE.md Static Hosting; T-64, T-62; parent https://github.com/dalini/nina-website/issues/2

## Requirement

- **Statement**: The VPS MUST send mail only through a local mail relay agent (e.g. nullmailer or msmtp with a queue) that runs under its own unprivileged account, never as root, and is the only account that can read the SMTP relay credential; the credential MUST be kept in a file owned by and readable only by that account; the agent MUST offer the local `sendmail` interface, through which the certificate renewal, the Instagram token refresh and the content check hand their alerts to Ives without reading the credential; mail MUST be delivered to Ives's address through the relay, and a message that cannot be delivered yet MUST stay queued for a later attempt.
- **Rationale**: Ives must learn about failed renewals, refreshes and content checks (SEC-HTTP-5, SEC-SECRET-3, SEC-CICD-4), and the relay credential is one more secret on the VPS that must belong to exactly one account (SEC-SECRET-1, T-62).
- **Assumptions**: Ives chooses the SMTP relay provider and creates its credential at implementation (follow-up recorded in `.planning/OPEN_QUESTIONS.md`, step 10 a third time)
- **Out of Scope**: Detecting the failures and writing the alert messages (REQ-HTTP-060, REQ-INSTAGRAM-050, REQ-PIPELINE-130); build failure emails, which GitHub Actions sends (REQ-PIPELINE-110); the accounts and secret files of the other jobs (REQ-HOST-030).
- **Design Traceability**: N/A
- **Architecture Traceability**: Static Hosting (emails Ives about failed renewals, refreshes and content checks through a local mail relay agent under its own account that alone holds the SMTP relay credential)
- **Security Traceability**: SEC-HTTP-5, SEC-SECRET-1, SEC-HOST-2

## Scope

- **Applies To**: Server-Side Application
- **Components**: Static Hosting
- **Interfaces / Operations**: Local `sendmail` interface; the mail relay agent's queue; SMTP submission to the relay provider over TLS; the relay credential file
- **Actors**: Maintainer (Ives); the renewal, token refresh and content check jobs
- **Preconditions**: REQ-HOST-030
- **Data Classification**: Restricted
- **Personal or Regulated Data**: Personal Data (Ives's email address as recipient)
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Confidentiality, Availability
- **Control Layers**: Architecture, Authorization, Availability
- **Threat References**: STRIDE Information Disclosure and Elevation of Privilege: SECURITY.md Threat Model v3 T-62; STRIDE Denial of Service: T-64 (an unnoticed failed renewal); CWE-522, CWE-732
- **Abuse / Misuse Case**: A flaw in the content check or the token refresh reads the relay credential and uses the relay to send mail in the site's name
- **Trust Boundary**: Inside the VPS: the alerting jobs ↔ the mail relay agent and its credential (SECURITY.md Analysis surface); VPS ↔ SMTP relay provider
- **Untrusted Inputs or Assertions**: Message text handed in by the jobs; responses of the SMTP relay
- **Authoritative Enforcement Point**: The VPS operating system (accounts, file permissions)
- **Independent Verification**: Checked from outside the account or process under test, against the live host or settings
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: OWASP Secrets Management Cheat Sheet (`REF-SECRETS`)
- **Mapping Basis**: Other: `REF-SECRETS` as cited in SECURITY.md SEC-SECRET-1; no practice IDs verified.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given the mail relay agent is installed, when a test message is handed to the local `sendmail` interface by the renewal job's, the deploy account's and the content check's accounts, then each message reaches Ives's address through the SMTP relay.
2. **AC-02 — Boundary or failure behavior**: Given the SMTP relay is unreachable or rejects the connection, when a message is handed to `sendmail`, then `sendmail` accepts it into the queue, the agent retries, and the message is delivered once the relay is reachable again.
3. **AC-03 — Boundary or failure behavior**: Given the web server's account, the OAuth helper's account, the content check's account, the backup account and the deploy account, when each tries to read the relay credential file, then access is denied; only the mail relay agent's account can read it, and no root-run service (the renewal job included) is configured to read it.
4. **AC-04 — Prohibited behavior**: Given the process list, when the mail relay agent runs, then it runs under its own non-root account; and the agent's configuration, the queue files readable by other accounts, the logs and the delivered messages contain no relay credential.
5. **AC-05 — Additional criterion**: Given the connection to the SMTP relay, when it is made, then it uses TLS with certificate validation, and a relay presenting an invalid certificate receives no credential.

## Failure Behavior

- **On Invalid Input**: A message the agent cannot parse is rejected by `sendmail` with a non-zero exit status, which the calling job logs
- **On Authentication Failure**: The relay rejects the credential: messages stay queued and the failure is logged; Ives finds it in the yearly SEC-HOST-1 review or when expected alerts do not arrive
- **On Authorization Failure**: Denied by the operating system or the platform; nothing changes
- **On Security-Decision Failure**: Deny by default: without a valid certificate from the relay, no credential is sent
- **On External Dependency Failure**: SMTP relay unreachable: messages stay queued and are retried
- **On System Error**: The agent stops with a non-zero exit status and leaves the queue in place for the next start
- **Logging / Audit**: The agent logs delivery attempts and results without the credential or message bodies
- **Alerting**: This issue is the alert path for SEC-HTTP-5, SEC-SECRET-3 and SEC-CICD-4

## Test Strategy

- **Unit Tests**: N/A: configuration of an existing mail relay agent, no first-party code beyond a permission check script
- **Integration Tests**: Test messages from each alerting account to a test mailbox; relay switched off, then on again (queued message delivered)
- **Security Tests**: Cross-account read attempts on the credential file; process list review; search of configuration, logs and queue for the test credential; relay with an invalid certificate
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:HOST-040/AC-01`; AC-02 → `test:HOST-040/AC-02`; AC-03 → `test:HOST-040/AC-03`; AC-04 → `test:HOST-040/AC-04`; AC-05 → `test:HOST-040/AC-05` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test VPS (or the VPS before go-live) with the accounts and file layout of REQ-HOST-030, a test SMTP relay account and a test mailbox

## Dependencies

- **Upstream Requirements**: REQ-HOST-030 (https://github.com/dalini/nina-website/issues/65)
- **Downstream Requirements**: REQ-HTTP-060 (https://github.com/dalini/nina-website/issues/77), REQ-PIPELINE-130 (https://github.com/dalini/nina-website/issues/94), REQ-INSTAGRAM-050 (https://github.com/dalini/nina-website/issues/97)
- **External Dependencies**: SMTP relay provider chosen by Ives; VPS provider in Germany
- **Dependency Assumptions**: The relay accepts authenticated submission over TLS
- **Failure Impact**: Without the relay, Ives receives no alert for failed renewals, token refreshes and content checks (T-64, T-58)

## Implementation Notes

- **Constraints**: Configuration and scripts kept in `dalini/nina-site`; the credential file is created by Ives on the VPS and never committed; runbooks in `docs/ops/` of `dalini/nina-website` hold no secrets and no personal data beyond the editors' names (SEC-SECRET-1); DEP-1 to DEP-8 apply to the mail relay package
- **Prohibited Approaches**: Running the agent as root; giving any job a copy of the relay credential or SMTP settings of its own; settings applied by hand without being kept in the reviewed configuration or runbook
- **Implementation Guidance**: Use a packaged mail relay agent with a queue (SEC-HTTP-5 names nullmailer and msmtp as examples); add its account to the permission check of REQ-HOST-030.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Opus 5.5 (`claude-opus-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: It places one more secret on a shared server, where the account, file-permission and queue-permission details decide whether every job can read it.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None. Decided in step 10 a third time (`.planning/OPEN_QUESTIONS.md`): PQ-56: a local mail relay agent under its own account alone holds the relay credential; the jobs hand mail to local `sendmail` (SEC-HTTP-5, SEC-SECRET-1).
