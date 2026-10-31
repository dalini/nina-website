## Metadata

- **ID**: REQ-HTTP-010
- **Title**: Serve nina-laaf.de over HTTPS only, redirect HTTP to HTTPS and send HSTS
- **Version**: 0.3.0
- **Status**: Draft
- **Owner**: Maintainer Ives (dalini)
- **Author**: Claude (step 9 third run, prompts/09-github-epic.md)
- **Last Updated**: 2026-10-31
- **Priority**: High
- **Requirement Type**: Security
- **Source / Parent**: SECURITY.md SEC-HTTP-1, `REF-ASVS-5`; REQUIREMENTS.md NFR-4; parent {{ISSUE_URL:REQ-HTTP-000}}

## Requirement

- **Statement**: Static Hosting MUST serve every page and asset of `nina-laaf.de` over HTTPS only, MUST redirect every HTTP request to HTTPS, and MUST send `Strict-Transport-Security: max-age=31536000; includeSubDomains` (no preload) on HTTPS responses.
- **Rationale**: Pages must not be altered or read in transit (T-28).
- **Assumptions**: None
- **Out of Scope**: `edit.` and `preview.` hosts (REQ-HTTP-040); certificate renewal (REQ-HTTP-060).
- **Design Traceability**: N/A
- **Architecture Traceability**: Static Hosting (serves over HTTPS with the SEC-HTTP headers)
- **Security Traceability**: SEC-HTTP-1

## Scope

- **Applies To**: Server-Side Application
- **Components**: Static Hosting
- **Interfaces / Operations**: All responses of `nina-laaf.de`
- **Actors**: Visitor (anonymous); maintainer (Ives)
- **Preconditions**: REQ-HOST-020
- **Data Classification**: Public
- **Personal or Regulated Data**: None
- **Jurisdiction / Regulatory Scope**: N/A

## Security Context

- **Security Objectives**: Integrity
- **Control Layers**: Architecture, Data Protection
- **Threat References**: STRIDE Tampering / Information Disclosure: SECURITY.md Threat Model v3 T-28; CWE-319
- **Abuse / Misuse Case**: N/A
- **Trust Boundary**: TB-1 (visitor browser ↔ Static Hosting)
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
- **Other**: IETF RFC 6797 (HTTP Strict Transport Security)
- **Mapping Basis**: Other: RFC 6797 defines HSTS.

## Acceptance Criteria

1. **AC-01 — Expected behavior**: Given any page path, when it is requested over HTTPS, then it is served with `Strict-Transport-Security: max-age=31536000; includeSubDomains` and no `preload`.
2. **AC-02 — Boundary or failure behavior**: Given any path, when it is requested over HTTP, then the response is a permanent redirect to the same path over HTTPS and carries no page content.
3. **AC-03 — Prohibited behavior**: Given the host, when it is scanned, then no page or asset is served over plain HTTP with status 200.

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
- **Integration Tests**: Header and redirect check against the test server and, after deploy, the live host
- **Security Tests**: TLS configuration scan; HTTP request matrix over sample paths and assets
- **Compliance Tests / Evidence**: N/A
- **Acceptance-Criteria Traceability**: AC-01 → `test:HTTP-010/AC-01`; AC-02 → `test:HTTP-010/AC-02`; AC-03 → `test:HTTP-010/AC-03` (test command `deno task test`, CLAUDE.md)
- **Coverage Target**: 80% line coverage of first-party code (CLAUDE.md Commands). Every AC-* has at least one automated test; every security decision and error path has a positive and a negative test.
- **Required Test Environment**: A test server running the same nginx configuration as the VPS, plus the live hosts after deploy

## Dependencies

- **Upstream Requirements**: REQ-HOST-020 ({{ISSUE_URL:REQ-HOST-020}})
- **Downstream Requirements**: REQ-HTTP-020 ({{ISSUE_URL:REQ-HTTP-020}}), REQ-HTTP-030 ({{ISSUE_URL:REQ-HTTP-030}}), REQ-HTTP-040 ({{ISSUE_URL:REQ-HTTP-040}}), REQ-HTTP-060 ({{ISSUE_URL:REQ-HTTP-060}}), REQ-HTTP-070 ({{ISSUE_URL:REQ-HTTP-070}})
- **External Dependencies**: VPS provider in Germany
- **Dependency Assumptions**: The VPS is reachable and administered by Ives (SEC-HOST-1)
- **Failure Impact**: If the VPS is down the site is unavailable (accepted, T-31)

## Implementation Notes

- **Constraints**: nginx on the rented VPS (ARCHITECTURE.md Static Hosting); DEP-1 to DEP-8 apply to every package added
- **Prohibited Approaches**: Settings applied by hand without being kept in the reviewed configuration
- **Implementation Guidance**: None beyond the sources cited above.
- **AI Development Guidance**: Follow CLAUDE.md and SECURITY.md; implement as one pull request (prompts/11-start-coding.md); a human reviews and merges every pull request. Recommended model: Claude Sonnet 5.5 (`claude-sonnet-5-5`; verified 2026-10-31, see ISSUE_PLAN.md Planning Rules), because: Host configuration and checks fully specified by SEC-HTTP-1.
- **Required Human Review**: Security review by Ives
- **Open Decisions**: None
