## Metadata

- **ID**: REQ-PRIV-020
- **Title**: Delete nginx access logs after 14 days and keep them readable only by the maintainer
- **Version**: 0.2.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Privacy
- **Source / Parent**: SECURITY.md SEC-PRIV-6, `REF-PC-2024`; parent https://github.com/dalini/nina-website/issues/2

## Requirement

- **Statement**: The nginx access logs of `nina-laaf.de`, `edit.nina-laaf.de` and `preview.nina-laaf.de` MAY contain full visitor IP addresses; they MUST be deleted after 14 days and MUST be readable only by the maintainer.
- **Rationale**: IP addresses are personal data; they are kept only for abuse handling (T-30).
- **Assumptions**: None
- **Out of Scope**: Describing the logs in the Datenschutzerklärung (content, REQ-LEGAL-010 review).
- **Design Traceability**: N/A
- **Architecture Traceability**: Static Hosting (keeps access logs as SEC-PRIV-6)
- **Security Traceability**: SEC-PRIV-6

## Scope

- **Applies To**: Server-Side Application
- **Components**: Static Hosting
- **Interfaces / Operations**: nginx log configuration and log rotation on the VPS
- **Actors**: Visitor (anonymous); maintainer (Ives)
- **Preconditions**: REQ-HOST-020
- **Data Classification**: Confidential
- **Personal or Regulated Data**: Personal Data (visitor IP addresses)
- **Jurisdiction / Regulatory Scope**: Germany / EU: GDPR

## Security Context

- **Security Objectives**: Privacy, Confidentiality
- **Control Layers**: Data Protection
- **Threat References**: STRIDE Information Disclosure: SECURITY.md Threat Model v3 T-30; CWE-732
- **Abuse / Misuse Case**: Another account on the VPS, or a leaked deploy key, reads visitor IP addresses from the logs
- **Trust Boundary**: TB-1 (visitor browser ↔ Static Hosting)
- **Untrusted Inputs or Assertions**: Visitor-controlled request fields written to the log
- **Authoritative Enforcement Point**: Static Hosting (nginx on the VPS)
- **Independent Verification**: Checked from outside the VPS against the live host
- **Zero Trust Relevance**: N/A

## Standards Alignment

- **OWASP ASVS 5.0.0**: Level 1 (SECURITY.md Security assurance target); ASVS identifiers are added once verified against the 5.0.0 release
- **OWASP AISVS 1.0**: N/A: no AI-enabled component
- **NIST SP 800-53 Rev. 5**: Not applicable (SECURITY.md Security assurance target)
- **NIST SP 800-207**: N/A
- **Regulatory**: GDPR (Regulation (EU) 2016/679); no article mapping is kept (SECURITY.md)
- **Other**: OWASP Top 10 Proactive Controls 2024 (`REF-PC-2024`)
- **Mapping Basis**: Other: `REF-PC-2024` as cited in SECURITY.md; Regulatory: GDPR, article not assessed.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given log rotation is configured, when a log file becomes older than 14 days, then it is deleted, including rotated and compressed copies.
2. **AC-02 — Boundary or failure behavior**: Given the deploy account, the nginx worker account and any other non-maintainer account, when they try to read the access logs, then access is denied.
3. **AC-03 — Prohibited behavior**: Given the VPS, when it is searched for copies of access logs, then none older than 14 days exists in any location.

## Failure Behavior

- **On Invalid Input**: Malformed requests are rejected by nginx with a 4xx status; no content changes
- **On Authentication Failure**: N/A: public responses need no sign-in
- **On Authorization Failure**: N/A
- **On Security-Decision Failure**: Deny by default: a host without the required configuration does not serve the site
- **On External Dependency Failure**: N/A
- **On System Error**: nginx refuses to start with an invalid configuration; the configuration is checked before it is reloaded, so the running configuration stays in place
- **Logging / Audit**: nginx access logs as SEC-PRIV-6 (REQ-PRIV-020)
- **Alerting**: N/A

## Test Strategy

- **Unit Tests**: N/A: configuration, no first-party code
- **Integration Tests**: Log rotation run with backdated files on the test server; permission checks per account
- **Security Tests**: Read attempts from the deploy and web server accounts
- **Compliance Tests / Evidence**: Evidence: log and rotation configuration and permission listing, for the Datenschutzerklärung review
- **Acceptance-Criteria Traceability**: AC-01 → `test:PRIV-020/AC-01`; AC-02 → `test:PRIV-020/AC-02`; AC-03 → `test:PRIV-020/AC-03` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test server running the same nginx configuration as the VPS, plus the live hosts after deploy

## Dependencies

- **Upstream Requirements**: REQ-HOST-020 (https://github.com/dalini/nina-website/issues/64)
- **Downstream Requirements**: REQ-AUTHN-040 (https://github.com/dalini/nina-website/issues/109)
- **External Dependencies**: VPS provider in Germany
- **Dependency Assumptions**: The VPS is reachable and administered by Ives (SEC-HOST-1)
- **Failure Impact**: If the VPS is down the site is unavailable (accepted, T-31)

## Implementation Notes

- **Constraints**: nginx on the rented VPS (ARCHITECTURE.md Static Hosting); DEP-1 to DEP-8 apply to every package added
- **Prohibited Approaches**: Settings applied by hand without being kept in the reviewed configuration
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Log rotation and file permissions with a clear retention value.
- **Required Human Review**: Security and privacy review by Ives
- **Open Decisions**: None
