## Metadata

- **ID**: REQ-HTTP-060
- **Title**: Renew the TLS certificates of all three hosts automatically from Let's Encrypt and email Ives about failed renewals
- **Version**: 0.3.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 fourth run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Reliability
- **Source / Parent**: SECURITY.md SEC-HTTP-5, `REF-ASVS-5`; parent {{ISSUE_URL:REQ-HTTP-000}}

## Requirement

- **Statement**: The TLS certificates of `nina-laaf.de`, `edit.nina-laaf.de` and `preview.nina-laaf.de` MUST be renewed automatically from Let's Encrypt (certbot or lego) before they expire; a failed renewal, or a certificate inside its renewal window that was not renewed, MUST be emailed to Ives with the host name, handed to the local `sendmail` interface of the mail relay agent (REQ-HOST-040); the renewal job MUST NOT read the SMTP relay credential.
- **Rationale**: With HSTS, an expired certificate makes the site and the Editing Interface unreachable (T-64).
- **Assumptions**: None
- **Out of Scope**: Installing the mail relay agent and holding the SMTP relay credential (REQ-HOST-040); the Instagram token refresh and content check alerts (REQ-INSTAGRAM-050, REQ-PIPELINE-130).
- **Design Traceability**: N/A
- **Architecture Traceability**: Static Hosting
- **Security Traceability**: SEC-HTTP-5, SEC-SECRET-1 (the relay credential belongs to the mail relay agent), SEC-HOST-2

## Scope

- **Applies To**: Server-Side Application
- **Components**: Static Hosting
- **Interfaces / Operations**: Certificate renewal job and expiry check on the VPS
- **Actors**: Visitor (anonymous); maintainer (Ives)
- **Preconditions**: REQ-HTTP-010, REQ-HTTP-040, REQ-HOST-040
- **Data Classification**: Public
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Availability
- **Control Layers**: Availability
- **Threat References**: STRIDE Denial of Service: SECURITY.md Threat Model v3 T-64
- **Abuse / Misuse Case**: N/A
- **Trust Boundary**: TB-1, TB-2
- **Untrusted Inputs or Assertions**: Responses of the certificate authority
- **Authoritative Enforcement Point**: Static Hosting (nginx on the VPS)
- **Independent Verification**: Checked from outside the VPS against the live host
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: N/A
- **Other**: N/A
- **Mapping Basis**: No external standard beyond ASVS Level 1.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given each host's certificate enters its renewal window, when the renewal job runs, then a new certificate is installed and served without downtime, and no email is sent.
2. **AC-02 — Boundary or failure behavior**: Given renewal is forced to fail (certificate authority unreachable), when the job runs, then an email naming the host and the failure reaches Ives's address through the local `sendmail` interface.
3. **AC-03 — Boundary or failure behavior**: Given a certificate inside its renewal window that was not renewed, when the expiry check runs against all three hosts, then Ives is emailed before the certificate expires, so no host reaches expiry without an alert.
4. **AC-04 — Prohibited behavior**: Given the renewal job and its alert hook, when their configuration, command line and logs are reviewed, then they read no relay credential file and contain no relay credential; the alert is handed only to the local `sendmail` interface.

## Failure Behavior

- **On Invalid Input**: Malformed requests are rejected by nginx with a 4xx status; no content changes
- **On Authentication Failure**: N/A: public responses need no sign-in
- **On Authorization Failure**: N/A
- **On Security-Decision Failure**: Deny by default: a host without the required configuration does not serve the site
- **On External Dependency Failure**: Certificate authority unreachable: renewal retries before expiry and the failure is emailed to Ives; SMTP relay unreachable: the mail relay agent keeps the message queued and retries (REQ-HOST-040)
- **On System Error**: nginx refuses to start with an invalid configuration; the configuration is checked before it is reloaded, so the running configuration stays in place
- **Logging / Audit**: nginx access logs as SEC-PRIV-6 (REQ-PRIV-020)
- **Alerting**: A failed renewal and a certificate left inside its renewal window are emailed to Ives through the local mail relay agent (REQ-HOST-040)

## Test Strategy

- **Unit Tests**: N/A: configuration, no first-party code
- **Integration Tests**: Forced renewal and forced failure on the test server; expiry check against all three hosts; the failure email arrives in a test mailbox through the mail relay agent
- **Security Tests**: Review of the renewal job's configuration and hook for any relay credential or SMTP setting; search of the job's logs for the test credential
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:HTTP-060/AC-01`; AC-02 → `test:HTTP-060/AC-02`; AC-03 → `test:HTTP-060/AC-03`; AC-04 → `test:HTTP-060/AC-04` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test server running the same nginx configuration as the VPS, plus the live hosts after deploy

## Dependencies

- **Upstream Requirements**: REQ-HTTP-010 ({{ISSUE_URL:REQ-HTTP-010}}), REQ-HTTP-040 ({{ISSUE_URL:REQ-HTTP-040}}), REQ-HOST-040 ({{ISSUE_URL:REQ-HOST-040}})
- **Downstream Requirements**: None
- **External Dependencies**: VPS provider in Germany
- **Dependency Assumptions**: The VPS is reachable and administered by Ives (SEC-HOST-1)
- **Failure Impact**: If the VPS is down the site is unavailable (accepted, T-31)

## Implementation Notes

- **Constraints**: nginx on the rented VPS (ARCHITECTURE.md Static Hosting); DEP-1 to DEP-8 apply to every package added
- **Prohibited Approaches**: Settings applied by hand without being kept in the reviewed configuration
- **Implementation Guidance**: Hand the alert to `sendmail` (REQ-HOST-040); do not configure SMTP in the renewal tool itself.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: A standard renewal job and expiry check that hands its alert to an existing local mail interface.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None. Decided in step 10 a third time (`.planning/OPEN_QUESTIONS.md`): PQ-56: VPS alerts go through a local mail relay agent that runs under its own account and alone holds the relay credential; the jobs hand mail to local `sendmail` (SEC-HTTP-5, SEC-SECRET-1). Applied in this issue.
