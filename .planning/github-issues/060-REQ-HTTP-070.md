## Metadata

- **ID**: REQ-HTTP-070
- **Title**: Limit requests and connections per IP address on all three hosts
- **Version**: 0.1.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 re-run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Reliability
- **Source / Parent**: ARCHITECTURE.md Static Hosting (limits requests and connections per IP); SECURITY.md T-31; parent {{ISSUE_URL:REQ-HTTP-000}}

## Requirement

- **Statement**: nginx MUST limit requests and connections per client IP address on `nina-laaf.de`, `edit.nina-laaf.de` and `preview.nina-laaf.de`, answering over-limit requests with an error status while other clients are still served.
- **Rationale**: Limits blunt small floods; a large flood taking the site down is accepted (T-31).
- **Assumptions**: None
- **Out of Scope**: Large distributed floods (accepted risk, T-31).
- **Design Traceability**: N/A
- **Architecture Traceability**: Static Hosting (limits requests and connections per IP)
- **Security Traceability**: N/A (decision recorded in SECURITY.md T-31)

## Scope

- **Applies To**: Server-Side Application
- **Components**: Static Hosting
- **Interfaces / Operations**: nginx rate and connection limits per host
- **Actors**: Visitor (anonymous); maintainer (Ives)
- **Preconditions**: REQ-HTTP-010, REQ-HTTP-040
- **Data Classification**: Public
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Availability
- **Control Layers**: Availability
- **Threat References**: STRIDE Denial of Service: SECURITY.md Threat Model v2 T-31; CWE-770
- **Abuse / Misuse Case**: One client opens many connections or sends rapid requests to the OAuth helper path or the preview site
- **Trust Boundary**: TB-1, TB-2
- **Untrusted Inputs or Assertions**: Visitor requests
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

1. **AC-01 — Expected behavior**: Given normal browsing of every page type, when a visitor loads pages and images, then no request is limited.
2. **AC-02 — Boundary or failure behavior**: Given one test client exceeding the request or connection limit, when it continues, then it receives an over-limit status while a second client is served normally.
3. **AC-03 — Prohibited behavior**: Given the limits, when they are reviewed, then each of the three hosts has both a request and a connection limit.

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
- **Integration Tests**: Load test from one client against the test server; parallel normal client
- **Security Tests**: Flood from one address against each host
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:HTTP-070/AC-01`; AC-02 → `test:HTTP-070/AC-02`; AC-03 → `test:HTTP-070/AC-03` (test command TO BE DECIDED by the first coding PR, CLAUDE.md)
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
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; NOT VERIFIED, see ISSUE_PLAN.md Planning Rules), because: Standard nginx limits once the values are set.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: PQ-42 (non-blocking): the limit values.
