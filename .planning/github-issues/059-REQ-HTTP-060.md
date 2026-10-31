## Metadata

- **ID**: REQ-HTTP-060
- **Title**: Renew the TLS certificates of all three hosts automatically and alert Ives when a renewal fails
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Reliability
- **Source / Parent**: SECURITY.md SEC-HTTP-5, `REF-ASVS-5`; parent {{ISSUE_URL:REQ-HTTP-000}}

## Requirement

- **Statement**: The TLS certificates of `nina-laaf.de`, `edit.nina-laaf.de` and `preview.nina-laaf.de` MUST be renewed automatically before they expire, and a failed renewal MUST alert Ives.
- **Rationale**: With HSTS, an expired certificate makes the site and the Editing Interface unreachable (T-64).
- **Assumptions**: None
- **Out of Scope**: None
- **Design Traceability**: N/A
- **Architecture Traceability**: Static Hosting
- **Security Traceability**: SEC-HTTP-5

## Scope

- **Applies To**: Server-Side Application
- **Components**: Static Hosting
- **Interfaces / Operations**: Certificate renewal job and expiry check on the VPS
- **Actors**: Visitor (anonymous); maintainer (Ives)
- **Preconditions**: REQ-HTTP-010, REQ-HTTP-040
- **Data Classification**: Public
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Availability
- **Control Layers**: Availability
- **Threat References**: STRIDE Denial of Service: SECURITY.md Threat Model v2 T-64
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

1. **AC-01 — Expected behavior**: Given each host's certificate enters its renewal window, when the renewal job runs, then a new certificate is installed and served without downtime.
2. **AC-02 — Boundary or failure behavior**: Given renewal is forced to fail (certificate authority unreachable), when the job runs, then Ives receives an alert naming the host.
3. **AC-03 — Prohibited behavior**: Given the expiry check, when it runs against all three hosts, then no host is allowed to reach expiry without an alert having been sent first.

## Failure Behavior

- **On Invalid Input**: Malformed requests are rejected by nginx with a 4xx status; no content changes
- **On Authentication Failure**: N/A: public responses need no sign-in
- **On Authorization Failure**: N/A
- **On Security-Decision Failure**: Deny by default: a host without the required configuration does not serve the site
- **On External Dependency Failure**: Certificate authority unreachable: renewal retries before expiry; continued failure alerts Ives
- **On System Error**: nginx refuses to start with an invalid configuration; the configuration is checked before it is reloaded, so the running configuration stays in place
- **Logging / Audit**: nginx access logs as SEC-PRIV-6 (REQ-PRIV-020)
- **Alerting**: A failed renewal, or a certificate within its renewal window that was not renewed, alerts Ives (by email through an SMTP relay, PQ-40, SEC-HTTP-5)

## Test Strategy

- **Unit Tests**: N/A: configuration, no first-party code
- **Integration Tests**: Forced renewal and forced failure on the test server; expiry check against all three hosts
- **Security Tests**: N/A
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:HTTP-060/AC-01`; AC-02 → `test:HTTP-060/AC-02`; AC-03 → `test:HTTP-060/AC-03` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test server running the same nginx configuration as the VPS, plus the live hosts after deploy

## Dependencies

- **Upstream Requirements**: REQ-HTTP-010 ({{ISSUE_URL:REQ-HTTP-010}}), REQ-HTTP-040 ({{ISSUE_URL:REQ-HTTP-040}})
- **Downstream Requirements**: None
- **External Dependencies**: VPS provider in Germany
- **Dependency Assumptions**: The VPS is reachable and administered by Ives (SEC-HOST-1)
- **Failure Impact**: If the VPS is down the site is unavailable (accepted, T-31)

## Implementation Notes

- **Constraints**: nginx on the rented VPS (ARCHITECTURE.md Static Hosting); DEP-1 to DEP-8 apply to every package added
- **Prohibited Approaches**: Settings applied by hand without being kept in the reviewed configuration
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; NOT VERIFIED, see ISSUE_PLAN.md Planning Rules), because: Standard renewal job and expiry check once the source and alert channel are chosen.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None. Answered in step 10 again (`.planning/OPEN_QUESTIONS.md`): PQ-40: Let's Encrypt; alerts emailed to Ives through an SMTP relay (SEC-HTTP-5).
